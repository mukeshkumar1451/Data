import logging
from typing import Dict, List

from azure.search.documents import SearchClient
from azure.search.documents.models import VectorizedQuery
from azure.core.credentials import AzureKeyCredential
from openai import AzureOpenAI

from config.config import get

logger = logging.getLogger(__name__)


class RetrievalIntelligenceAgent:

    # =========================================================
    # INIT
    # =========================================================
    def __init__(self):

        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )

        self.embed_model = get("EMBEDDING_MODEL")
        self.chat_model = get("CHAT_MODEL")

        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY")),
        )

    # =========================================================
    # EMBEDDING
    # =========================================================
    def _embed(self, text: str) -> List[float]:
        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=text
        )
        return emb.data[0].embedding

    # =========================================================
    # HYBRID VECTOR SEARCH
    # =========================================================
    def _vector_retrieve(self, query_text: str, channel: str, ktype: str, topk: int):

        vector_query = VectorizedQuery(
            vector=self._embed(query_text),
            fields="embedding",
            k_nearest_neighbors=topk
        )

        filter_query = f"knowledgeType eq '{ktype}' and channels/any(c: c eq '{channel}')"

        results = self.search_client.search(
            search_text=query_text,
            vector_queries=[vector_query],
            filter=filter_query,
            select=["testCaseId", "content", "knowledgeType"],
            top=topk
        )

        return list(results)

    # =========================================================
    # RERANK TESTCASES
    # =========================================================
    def _rerank_testcases(self, query_text: str, docs: List[Dict]):

        if not docs:
            return []

        combined = ""
        for idx, d in enumerate(docs, start=1):
            combined += f"""
Document {idx}
TestCaseId: {d.get('testCaseId')}
Content:
{d.get('content')}
---------------------
"""

        prompt = f"""
You are a QA expert.

User Story:
{query_text}

Rank the below testcases from MOST relevant to LEAST relevant.
Return ONLY the TestCaseId list.
Do NOT explain.

{combined}
"""

        resp = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        ranking = resp.choices[0].message.content.strip().splitlines()
        id_map = {d["testCaseId"]: d for d in docs if d.get("testCaseId")}

        ordered = [id_map[x] for x in ranking if x in id_map]

        return ordered[:10]

    # =========================================================
    # SETUP INFERENCE (CHANNEL AWARE RAG)
    # =========================================================
    def _infer_setup(self, channel: str, query_text: str, knowledge_docs: List[Dict]):

        knowledge_context = ""
        for d in knowledge_docs[:8]:
            knowledge_context += f"\n---\n{d.get('content','')}\n"

        prompt = f"""
You are a Mortgage Product SME.

Each channel represents a DIFFERENT business workflow.

Retail (RTL) = loan officer + borrower interaction
Wholesale (WHL) = broker originated
DTC = borrower self-service digital flow
CL1 = correspondent purchase after origination

You MUST infer a setup NATURAL to THAT workflow only.

CHANNEL:
{channel}

CHANNEL WORKFLOW:
{query_text}

SYSTEM KNOWLEDGE:
{knowledge_context}

Rules:
• DO NOT reuse same loan setup across channels
• Prefer production-realistic loan
• Choose stage where this workflow logically occurs
• Broker channels rarely behave like retail
• Correspondent rarely originates new loans

Return ONLY:

Loan Purpose:
Loan Type:
Product:
Loan Stage:
Existing Conditions:
"""

        resp = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        return resp.choices[0].message.content.strip()

    # =========================================================
    # BUILD CONTEXT PER CHANNEL
    # =========================================================
    def _build_channel_context(self, channel_text: str, channel: str):

        logger.info(f"🔎 Hybrid retrieval for {channel} using channel specific workflow")

        flows = self._vector_retrieve(channel_text, channel, "e2e_pdf_flow", 6)
        rules = self._vector_retrieve(channel_text, channel, "e2e_excel", 6)
        guidelines = self._vector_retrieve(channel_text, channel, "step_guideline", 6)

        tests = self._vector_retrieve(channel_text, channel, "testcase", 40)
        reranked_tests = self._rerank_testcases(channel_text, tests)

        setup = self._infer_setup(channel, channel_text, flows + rules + reranked_tests[:5])

        logger.info(f"\n Inferred Setup for {channel}:\n{setup}\n")

        return {
            "flow": flows,
            "rules": rules,
            "guidelines": guidelines,
            "tests": reranked_tests,
            "setup": setup
        }

    # =========================================================
    # LANGGRAPH ENTRY
    # =========================================================
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 Retrieval Intelligence Agent (Channel Aware RAG)")

        retrieved_docs = {}

        # channel specific story produced by ADO agent
        channel_contexts = state.get("channel_context", {})

        for channel in state["channels"]:

            # 🔥 KEY FIX — channel specific retrieval
            channel_text = channel_contexts.get(channel)

            if not channel_text or len(channel_text.strip()) < 30:
                logger.warning(f"⚠️ No specific context for {channel}, falling back to full story")

                channel_text = f"""
User Story: {state['user_story']}
Description: {state['description']}
Acceptance Criteria: {state['acceptance_criteria']}
"""

            retrieved_docs[channel] = self._build_channel_context(channel_text, channel)

        state["retrieved_docs"] = retrieved_docs
        state["channel_setup"] = {ch: retrieved_docs[ch]["setup"] for ch in retrieved_docs}

        logger.info(f"\n final channel setups: {state['channel_setup']} \n")

        return state
