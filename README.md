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

    # ---------------------------------------------------------
    # EMBEDDING
    # ---------------------------------------------------------
    def _embed(self, text: str) -> List[float]:
        safe_text = text[:8000]
        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=safe_text
        )
        return emb.data[0].embedding

    # ---------------------------------------------------------
    # VECTOR RETRIEVE
    # ---------------------------------------------------------
    def _vector_retrieve(self, query_text: str, channel: str, topk: int):

        vector_query = VectorizedQuery(
            vector=self._embed(query_text),
            fields="embedding",
            k_nearest_neighbors=topk
        )

        filter_query = f"channels/any(c: c eq '{channel}') and knowledgeType eq 'testcase'"

        results = list(self.search_client.search(
            search_text=query_text,
            vector_queries=[vector_query],
            filter=filter_query,
            select=["testCaseId", "content"],
            top=topk
        ))

        logger.info(f"📊 {channel} → Retrieved {len(results)} docs")

        return results

    # ---------------------------------------------------------
    # RERANK WITH MINIMUM CONTEXT PROTECTION
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
{d.get('content')}
---------------------
"""

        prompt = f"""
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

        # 🔥 Stability Protection
        if len(ordered) < 3:
            logger.warning("⚠️ Rerank returned < 3 docs, using raw top 5")
            return docs[:5]

        return ordered[:5]

    # ---------------------------------------------------------
    # CHANNEL SANITIZATION
    # ---------------------------------------------------------
    def _sanitize_docs(self, channel: str, docs: List[Dict]):

        if channel == "RTL":
            return [
                d for d in docs
                if "Mortgage Broker" not in d.get("content", "")
            ]

        return docs

    # ---------------------------------------------------------
    # SETUP INFERENCE (SANITIZED)
    # ---------------------------------------------------------
    def _infer_setup(self, channel: str, channel_text: str, docs: List[Dict]):

        docs = self._sanitize_docs(channel, docs)

        knowledge_context = "\n".join(
            d.get("content", "") for d in docs[:5]
        )

        prompt = f"""
You are a mortgage domain SME.

Channel: {channel}

Workflow:
{channel_text}

Knowledge:
{knowledge_context}

Infer realistic setup.

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
    # BUILD CHANNEL CONTEXT
    # ---------------------------------------------------------
    def _build_channel_context(self, channel_text: str, channel: str):

        tests = self._vector_retrieve(channel_text, channel, 40)

        reranked = self._rerank_testcases(channel_text, tests)

        sanitized = self._sanitize_docs(channel, reranked)

        setup = self._infer_setup(channel, channel_text, sanitized)

        return {
            "tests": sanitized,
            "setup": setup
        }

    # ---------------------------------------------------------
    # RUN
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 Retrieval Intelligence Agent (Stable Mode)")

        retrieved_docs = {}

        full_story = f"""
User Story: {state['user_story']}
Description: {state['description']}
Acceptance Criteria: {state['acceptance_criteria']}
"""

        for channel in state["channels"]:
            retrieved_docs[channel] = self._build_channel_context(full_story, channel)

        state["retrieved_docs"] = retrieved_docs
        state["channel_setup"] = {
            ch: retrieved_docs[ch]["setup"]
            for ch in retrieved_docs
        }

        return state
