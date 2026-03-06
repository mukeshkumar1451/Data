import logging
from typing import Dict, List
import json
import os

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

        text = text[:2000]

        response = self.openai.embeddings.create(
            model=self.embed_model,
            input=text
        )

        return response.data[0].embedding

    # ---------------------------------------------------------
    # HYBRID SEARCH
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

        self._save_log(channel, [
            {"testCaseId": r["testCaseId"], "content": r["content"]}
            for r in results
        ])

        return results

    # ---------------------------------------------------------
    # SAVE RETRIEVAL LOG
    # ---------------------------------------------------------
    def _save_log(self, channel: str, data: List[Dict]):

        log_dir = "logs"
        os.makedirs(log_dir, exist_ok=True)

        log_file = os.path.join(
            log_dir,
            f"{channel}_retrieval_log.json"
        )

        with open(log_file, "w", encoding="utf-8") as f:
            json.dump(data, f, ensure_ascii=False, indent=4)

    # ---------------------------------------------------------
    # PRECONDITION EXTRACTION
    # ---------------------------------------------------------
    def _extract_precondition(self, content: str) -> str:

        lines = content.splitlines()
        capture = False
        collected = []

        for line in lines:

            lower = line.lower().strip()

            if (
                "pre-condition" in lower or
                "precondition" in lower or
                "pre condition" in lower
            ):
                capture = True
                collected.append(line)
                continue

            if capture and (
                lower.startswith("step") or
                "test steps" in lower or
                "=========== test steps" in lower
            ):
                break

            if capture:
                collected.append(line)

        result = "\n".join(collected).strip()

        if result.lower().startswith(("pre-condition", "precondition", "pre condition")):
            lines = result.splitlines()
            result = "\n".join(lines[1:]).strip()

        return result

    # ---------------------------------------------------------
    # LLM RERANK
    # ---------------------------------------------------------
    def _rerank(self, story_text: str, docs: List[Dict]) -> List[Dict]:

        if not docs:
            return []

        combined = ""

        for idx, d in enumerate(docs, 1):
            combined += f"\nDocument {idx}\n{d.get('content')[:1500]}\n"

        prompt = f"""
You are ranking historical test cases.

User Story:
{story_text}

Rank the documents by relevance to the user story.

Return ONLY document numbers separated by space.

Example:
3 1 4 2

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

        logger.info(f"Reranked docs → {len(ordered_docs)}")

        return ordered_docs if ordered_docs else docs

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 Retrieval Agent Running")

        channel_context = {}
        selected_preconditions = {}

        # -----------------------------------------------------
        # BUILD QUERY FROM STORY DATA
        # -----------------------------------------------------
        query_text = f"""
Title:
{state.get("title","")}

Description:
{state.get("description","")}

Acceptance Criteria:
{state.get("acceptance_criteria","")}
"""

        for channel in state["channels"]:

            docs = self._hybrid_search(query_text, channel)

            reranked_docs = self._rerank(query_text, docs)[:5]

            best_precondition = ""
            historical_steps = ""

            for doc in reranked_docs:

                content = doc.get("content", "")

                if not best_precondition:

                    extracted = self._extract_precondition(content)

                    if extracted:
                        best_precondition = extracted

                historical_steps += "\n" + content[:600]

                if best_precondition:
                    break

            if not best_precondition and reranked_docs:

                logger.warning(
                    f"{channel} → No precondition found. Using fallback."
                )

                best_precondition = "Precondition not found in historical data."

            channel_context[channel] = {
                "precondition": best_precondition,
                "historical_steps": historical_steps[:4000]
            }

            selected_preconditions[channel] = best_precondition

        state["channel_context"] = channel_context
        state["selected_preconditions"] = selected_preconditions

        logger.info("✅ Retrieval Completed")

        return state
