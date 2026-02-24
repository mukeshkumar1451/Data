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

        # Azure AI Search
        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY")),
        )

    # ---------------------------------------------------------
    # Embed Query
    # ---------------------------------------------------------
    def _embed(self, text: str) -> List[float]:

        response = self.openai.embeddings.create(
            model=self.embed_model,
            input=text[:8000]
        )

        return response.data[0].embedding

    # ---------------------------------------------------------
    # Hybrid Search (Vector + Keyword)
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

        lines = content.splitlines()
        capture = False
        collected = []

        for line in lines:
            lower = line.lower()

            # Detect precondition header variations
            if (
                "pre-condition" in lower or
                "precondition" in lower or
                "pre condition" in lower
            ):
                capture = True
                collected.append(line)
                continue

            # Stop when steps begin
            if capture and line.strip().lower().startswith("step"):
                break

            if capture:
                collected.append(line)
                

        return "\n".join(collected).strip()


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
Return only numbers in order separated by space.

Story:
{story_text}

Documents:
{combined}
"""

        response = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        ranking_text = response.choices[0].message.content.strip()
        ranking_tokens = ranking_text.split()

        ordered_docs = []

        for token in ranking_tokens:
            if token.isdigit():
                idx = int(token) - 1
                if 0 <= idx < len(docs):
                    ordered_docs.append(docs[idx])

        return ordered_docs if ordered_docs else docs

    # ---------------------------------------------------------
    # Main Execution
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 Retrieval Agent Running")

        full_story = f"""
User Story: {state['user_story']}
Description: {state['description']}
Acceptance Criteria: {state['acceptance_criteria']}
"""

        channel_context = {}
        selected_preconditions = {}

        for channel in state["channels"]:

            docs = self._hybrid_search(full_story, channel)

            reranked_docs = self._rerank(full_story, docs)

            best_precondition = ""
            historical_steps = ""

            for doc in reranked_docs:

                content = doc.get("content", "")

                if not best_precondition:
                    extracted = self._extract_precondition(content)
                    if extracted:
                        best_precondition = extracted

                historical_steps += "\n" + content[:1000]

                if best_precondition:
                    break

            channel_context[channel] = {
                "precondition": best_precondition,
                "historical_steps": historical_steps[:4000]
            }

            selected_preconditions[channel] = best_precondition

        # 🔥 Store BOTH structured context and flat map
        state["channel_context"] = channel_context
        state["selected_preconditions"] = selected_preconditions
        
        logger.info(f"{channel} → Selected Precondition:\n{best_precondition}\n")

        logger.info("✅ Retrieval Completed")
        return state
