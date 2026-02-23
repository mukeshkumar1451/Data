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

        safe_text = text[:8000]  # token safety

        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=safe_text
        )

        return emb.data[0].embedding

    # =========================================================
    # HYBRID VECTOR SEARCH (CHANNEL FILTERED)
    # =========================================================
    def _vector_retrieve(self, query_text: str, channel: str, topk: int):

        logger.info(f"🔎 Running vector retrieval for {channel}")

        vector_query = VectorizedQuery(
            vector=self._embed(query_text),
            fields="embedding",
            k_nearest_neighbors=topk
        )

        filter_query = f"channels/any(c: c eq '{channel}')"

        results = list(
            self.search_client.search(
                search_text=query_text,
                vector_queries=[vector_query],
                filter=filter_query,
                select=["testCaseId", "content"],
                top=topk
            )
        )

        logger.info(f"📊 Channel {channel} → Retrieved {len(results)} documents")

        for r in results:
            logger.info(f"   ↳ {channel} → {r.get('testCaseId')}")

        return results

    # =========================================================
    # RERANK TESTCASES
    # =========================================================
    def _rerank_testcases(self, query_text: str, docs: List[Dict]):

        if not docs:
            logger.warning("⚠️ No documents to rerank")
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
You are a senior QA analyst.

Rank the below testcases by relevance to this workflow.

Workflow:
{query_text}

Return ONLY ordered TestCaseId values.
Do not explain.

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

        logger.info(f"✅ Reranked {len(ordered)} testcases")

        return ordered[:10]  # limit to top 10

    # =========================================================
    # CHANNEL AWARE SETUP INFERENCE
    # =========================================================
    def _infer_setup(self, channel: str, query_text: str, knowledge_docs: List[Dict]):

        knowledge_context = "\n".join(
            d.get("content", "") for d in knowledge_docs[:5]
        )

        prompt = f"""
You are a mortgage domain SME.

Each channel represents a DIFFERENT workflow:

RTL → Loan officer + borrower interaction
WHL → Broker originated workflow
DTC → Borrower self-service digital workflow
CL1 → Correspondent purchase workflow

Infer a REALISTIC loan setup NATURAL to THIS channel.

CHANNEL:
{channel}

STORY CONTEXT:
{query_text}

SYSTEM KNOWLEDGE:
{knowledge_context}

Rules:
• DO NOT reuse same setup across channels
• Broker ≠ Retail
• Correspondent ≠ Origination
• Choose realistic lifecycle stage

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

        setup = resp.choices[0].message.content.strip()

        logger.info(f"\n📌 Inferred Setup for {channel}:\n{setup}\n")

        return setup

    # =========================================================
    # BUILD CHANNEL CONTEXT
    # =========================================================
    def _build_channel_context(self, query_text: str, channel: str):

        logger.info(f"🔎 Building retrieval context for {channel}")

        tests = self._vector_retrieve(query_text, channel, 40)

        reranked_tests = self._rerank_testcases(query_text, tests)

        setup = self._infer_setup(channel, query_text, reranked_tests)

        return {
            "tests": reranked_tests,
            "setup": setup,
            "flow": [],
            "rules": [],
            "guidelines": []
        }

    # =========================================================
    # LANGGRAPH ENTRY
    # =========================================================
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 Retrieval Intelligence Agent (Stable Mode)")

        retrieved_docs = {}

        # 🔥 Use FULL STORY always (no LLM split)
        query_text = f"""
User Story: {state['user_story']}

Description:
{state['description']}

Acceptance Criteria:
{state['acceptance_criteria']}
"""

        for channel in state["channels"]:
            retrieved_docs[channel] = self._build_channel_context(query_text, channel)

        state["retrieved_docs"] = retrieved_docs
        state["channel_setup"] = {
            ch: retrieved_docs[ch]["setup"]
            for ch in retrieved_docs
        }

        logger.info("✅ Final Channel Setups Generated")

        return state
