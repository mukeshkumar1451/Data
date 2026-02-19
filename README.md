
import logging
from typing import Dict, List

from azure.search.documents import SearchClient
from azure.search.documents.models import VectorizedQuery
from azure.core.credentials import AzureKeyCredential
from openai import AzureOpenAI

from config.config import get

logger = logging.getLogger(__name__)


class RetrievalIntelligenceAgent:

    def __init__(self):
        # Azure OpenAI
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )

        self.embed_model = get("EMBEDDING_MODEL")
        self.chat_model = get("CHAT_MODEL")

        # Azure Search
        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY")),
        )

    # ---------------------------------------------------------
    # Embedding
    # ---------------------------------------------------------
    def _embed(self, text: str) -> List[float]:
        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=text
        )
        return emb.data[0].embedding

    # ---------------------------------------------------------
    # Generic Vector Retrieval
    # ---------------------------------------------------------
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

    # ---------------------------------------------------------
    # Rerank ONLY testcases
    # ---------------------------------------------------------
    def _rerank_testcases(self, query_text: str, docs: List[Dict]):
        if not docs:
            return []

        combined = ""
        for idx, d in enumerate(docs, start=1):
            combined += f"""
Document {idx}
TestCaseId: {d.get('testCaseId')}
Content:
{d['content']}
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

    # ---------------------------------------------------------
    # Convert retrieved docs → readable context
    # ---------------------------------------------------------
    def _knowledge_to_text(self, docs: List[Dict]) -> str:
        text = ""
        for d in docs:
            if not d:
                continue
            text += "\n" + d.get("content", "")
        return text[:12000]  # token safety

    # ---------------------------------------------------------
    # REAL SETUP INFERENCE (RAG POWERED)
    # ---------------------------------------------------------
    def _infer_setup(self, channel: str, query_text: str, knowledge_docs: List[Dict]):
        # Build knowledge context from retrieved flows & rules
        knowledge_context = ""
        for d in knowledge_docs[:8]:
            knowledge_context += f"\n---\n{d['content']}\n"

        prompt = f"""
You are a Mortgage Domain Expert QA.

Different channels DO NOT use identical loan types.
You must infer the MOST REALISTIC loan setup based on system workflow.

CHANNEL: {channel}

USER STORY:
{query_text}

SYSTEM KNOWLEDGE (authoritative):
{knowledge_context}

Rules:
• Choose the loan type that naturally occurs in this channel's lifecycle
• Retail ≠ Wholesale ≠ DTC ≠ Broker behavior
• Prefer realistic production setup, not generic coverage
• DO NOT pick same loan for every channel unless evidence proves it

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

    # ---------------------------------------------------------
    # Build final context per channel
    # ---------------------------------------------------------
    def _build_channel_context(self, query_text: str, channel: str):
        logger.info(f"🔎 Hybrid retrieval for {channel}")

        flows = self._vector_retrieve(query_text, channel, "e2e_pdf_flow", 6)
        rules = self._vector_retrieve(query_text, channel, "e2e_excel", 6)
        guidelines = self._vector_retrieve(query_text, channel, "step_guideline", 6)
        tests = self._vector_retrieve(query_text, channel, "testcase", 40)
        reranked_tests = self._rerank_testcases(query_text, tests)

        #  PASS KNOWLEDGE INTO SETUP INFERENCE
        setup = self._infer_setup(channel, query_text, flows + rules + reranked_tests[:5])

        logger.info(f"\n Inferred Setup for {channel}:\n{setup}\n")

        return {
            "flow": flows,
            "rules": rules,
            "guidelines": guidelines,
            "tests": reranked_tests,
            "setup": setup
        }

    # ---------------------------------------------------------
    # LangGraph Entry
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:
        logger.info(" Retrieval Intelligence Agent (Hybrid Mode + Setup Inference)")

        query_text = f"""
User Story: {state['user_story']}
Description: {state['description']}
Acceptance Criteria: {state['acceptance_criteria']}
"""

        retrieved_docs = {}

        for channel in state["channels"]:
            retrieved_docs[channel] = self._build_channel_context(query_text, channel)

        state["retrieved_docs"] = retrieved_docs
        state["channel_setup"] = {ch: retrieved_docs[ch]["setup"] for ch in retrieved_docs}

        logger.info(f"\n final channel setups: {state['channel_setup']} \n")

        return state

