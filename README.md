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
    # Embed Query
    # ---------------------------------------------------------
    def _embed(self, text: str) -> List[float]:

        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=text[:8000]
        )

        return emb.data[0].embedding

    # ---------------------------------------------------------
    # Hybrid Search
    # ---------------------------------------------------------
    def _hybrid_search(self, query_text: str, channel: str, topk: int = 20):

        vector_query = VectorizedQuery(
            vector=self._embed(query_text),
            fields="embedding",
            k_nearest_neighbors=topk
        )

        filter_query = f"channels/any(c: c eq '{channel}')"

        results = list(self.search_client.search(
            search_text=query_text,
            vector_queries=[vector_query],
            filter=filter_query,
            select=["testCaseId", "content"],
            top=topk
        ))

        logger.info(f"{channel} → Retrieved {len(results)} docs")

        return results

    # ---------------------------------------------------------
    # Extract Precondition Block
    # ---------------------------------------------------------
    def _extract_precondition(self, content: str) -> str:

        lower = content.lower()

        if "pre-condition" in lower:

            start = lower.index("pre-condition")
            block = content[start:]

            if "Step 01" in block:
                block = block.split("Step 01")[0]

            return block.strip()

        return ""

    # ---------------------------------------------------------
    # LLM Rerank
    # ---------------------------------------------------------
    def _rerank(self, story_text: str, docs: List[Dict]) -> List[Dict]:

        if not docs:
            return []

        combined = ""
        for idx, d in enumerate(docs, 1):
            combined += f"\nDoc {idx}\n{d.get('content')[:1500]}\n"

        prompt = f"""
Rank the below documents by relevance to this story.
Return only numbers in order.

Story:
{story_text}

Documents:
{combined}
"""

        resp = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        ranking = resp.choices[0].message.content.strip().split()

        ordered = []
        for r in ranking:
            if r.isdigit():
                idx = int(r) - 1
                if 0 <= idx < len(docs):
                    ordered.append(docs[idx])

        return ordered if ordered else docs

    # ---------------------------------------------------------
    # Main Entry
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 Retrieval Agent Running")

        full_story = f"""
User Story: {state['user_story']}
Description: {state['description']}
Acceptance Criteria: {state['acceptance_criteria']}
"""

        channel_context = {}

        for channel in state["channels"]:

            docs = self._hybrid_search(full_story, channel)

            reranked = self._rerank(full_story, docs)

            # Select Best Precondition
            selected_precondition = ""
            historical_steps = ""

            for d in reranked:

                content = d.get("content", "")

                if not selected_precondition:
                    pre = self._extract_precondition(content)
                    if pre:
                        selected_precondition = pre

                historical_steps += "\n" + content[:1000]

                if selected_precondition:
                    break

            channel_context[channel] = {
                "precondition": selected_precondition,
                "historical_steps": historical_steps[:4000]
            }

        state["channel_context"] = channel_context

        logger.info("✅ Retrieval Completed")

        return state
