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

        safe_text = text[:8000]

        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=safe_text
        )

        return emb.data[0].embedding

    # =========================================================
    # GENERIC VECTOR RETRIEVAL
    # =========================================================
    def _vector_retrieve(
        self,
        query_text: str,
        channel: str,
        topk: int,
        doc_type: str = None
    ):

        logger.info(f"🔎 Retrieving {doc_type or 'documents'} for {channel}")

        vector_query = VectorizedQuery(
            vector=self._embed(query_text),
            fields="embedding",
            k_nearest_neighbors=topk
        )

        if doc_type:
            filter_query = (
                f"channels/any(c: c eq '{channel}') "
                f"and docType eq '{doc_type}'"
            )
        else:
            filter_query = f"channels/any(c: c eq '{channel}')"

        results = list(self.search_client.search(
            search_text=query_text,
            vector_queries=[vector_query],
            filter=filter_query,
            select=["testCaseId", "content", "docType"],
            top=topk
        ))

        logger.info(f"📊 {channel} → Retrieved {len(results)} {doc_type or ''}")

        return results

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
You are a QA analyst.

Rank the below testcases by relevance to this workflow.

Workflow:
{query_text}

Return ONLY ordered TestCaseId values.
Do not explain.
"""

        resp = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[{"role": "user", "content": prompt + combined}],
            temperature=0
        )

        ranking = resp.choices[0].message.content.strip().splitlines()

        id_map = {d["testCaseId"]: d for d in docs if d.get("testCaseId")}

        ordered = [id_map[x] for x in ranking if x in id_map]

        if len(ordered) < 3:
            logger.warning("⚠️ Rerank weak → using top 5 raw docs")
            return docs[:5]

        return ordered[:5]

    # =========================================================
    # RTL SANITIZATION
    # =========================================================
    def _sanitize_docs(self, channel: str, docs: List[Dict]):

        if channel == "RTL":
            logger.info("🧹 Removing broker content for RTL")

            return [
                d for d in docs
                if "Mortgage Broker" not in d.get("content", "")
                and "Broker License" not in d.get("content", "")
                and "Broker Fee" not in d.get("content", "")
            ]

        return docs

    # =========================================================
    # BUILD CHANNEL CONTEXT
    # =========================================================
    def _build_channel_context(self, full_story: str, channel: str):

        # 1️⃣ Retrieve Testcases
        tests = self._vector_retrieve(
            full_story,
            channel,
            topk=40,
            doc_type="testcase"
        )

        tests = self._sanitize_docs(channel, tests)

        reranked = self._rerank_testcases(full_story, tests)

        # 2️⃣ Retrieve Preconditions (NO LLM)
        preconditions = self._vector_retrieve(
            full_story,
            channel,
            topk=3,
            doc_type="precondition"
        )

        precondition_text = "\n".join(
            p.get("content", "") for p in preconditions
        )

        return {
            "tests": reranked,
            "precondition": precondition_text.strip()
        }

    # =========================================================
    # LANGGRAPH ENTRY
    # =========================================================
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 Retrieval Intelligence Agent (Deterministic Mode)")

        retrieved_docs = {}

        full_story = f"""
User Story: {state['user_story']}
Description: {state['description']}
Acceptance Criteria: {state['acceptance_criteria']}
"""

        for channel in state["channels"]:
            retrieved_docs[channel] = self._build_channel_context(
                full_story,
                channel
            )

        state["retrieved_docs"] = retrieved_docs

        # 🔥 Setup now comes from retrieved preconditions
        state["channel_setup"] = {
            ch: retrieved_docs[ch]["precondition"]
            for ch in retrieved_docs
        }

        logger.info("✅ Retrieval completed (No LLM setup inference)")

        return state
