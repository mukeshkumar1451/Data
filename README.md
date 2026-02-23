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
            input=text[:8000]  # token safety
        )
        return emb.data[0].embedding

    # =========================================================
    # HYBRID VECTOR SEARCH
    # =========================================================
    def _vector_retrieve(self, query_text: str, channel: str, topk: int):
        

        vector_query = VectorizedQuery(
            vector=self._embed(query_text),
            fields="embedding",
            k_nearest_neighbors=topk
        )

        filter_query = f"channels/any(c: c eq '{channel}')"

        results = self.search_client.search(
            search_text=query_text,
            vector_queries=[vector_query],
            filter=filter_query,
            select=["testCaseId", "content"],
            top=topk
        )
        
        for r in results:
            logger.debug(f"Retrieved for channel {channel}: {r.get('testCaseId')} ")

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
Rank relevance to this workflow:

{query_text}

Return ordered TestCaseId only.
{combined}
"""

        resp = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        ranking = resp.choices[0].message.content.strip().splitlines()
        id_map = {d["testCaseId"]: d for d in docs if d.get("testCaseId")}

        return [id_map[x] for x in ranking if x in id_map][:10]

    # =========================================================
    # SETUP INFERENCE (CHANNEL AWARE RAG)
    # =========================================================
    def _infer_setup(self, channel: str, channel_text: str, knowledge_docs: List[Dict]):

        knowledge_context = "\n".join(d.get("content","") for d in knowledge_docs[:6])

        prompt = f"""
You are a mortgage domain expert.

Infer a REALISTIC loan setup for THIS channel workflow only.

Channel: {channel}

Workflow Meaning:
{channel_text}

System Knowledge:
{knowledge_context}

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

        logger.info(f"🔎 Retrieval for {channel} using channel workflow")

        tests = self._vector_retrieve(channel_text, channel, 40)
        reranked_tests = self._rerank_testcases(channel_text, tests)

        setup = self._infer_setup(channel, channel_text, reranked_tests)

        logger.info(f"\n Inferred Setup for {channel}:\n{setup}\n")

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

        logger.info("🚀 Retrieval Intelligence Agent (Channel Aware RAG)")

        retrieved_docs = {}

        # 🔥 CORRECT KEY
        channel_contexts = state.get("channel_context_map", {})

        for channel in state["channels"]:

            channel_text = channel_contexts.get(channel, "").strip()

            # smarter fallback (not full story)
            if len(channel_text) < 20:
                logger.warning(f"⚠️ Weak context for {channel}, using title only")
                channel_text = state["user_story"]

            retrieved_docs[channel] = self._build_channel_context(channel_text, channel)

        state["retrieved_docs"] = retrieved_docs
        state["channel_setup"] = {ch: retrieved_docs[ch]["setup"] for ch in retrieved_docs}

      #  logger.info(f"\n final channel setups: {state['channel_setup']} \n")

        return state
