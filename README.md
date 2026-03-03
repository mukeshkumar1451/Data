import logging
import json
import os
from typing import Dict, List
from datetime import datetime

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

        self.log_dir = get("RETRIEVAL_LOG_DIR")
        os.makedirs(self.log_dir, exist_ok=True)

    # ---------------------------------------------------------
    # Embed Title
    # ---------------------------------------------------------
    def _embed(self, text: str) -> List[float]:
        response = self.openai.embeddings.create(
            model=self.embed_model,
            input=text[:8000]
        )
        return response.data[0].embedding

    # ---------------------------------------------------------
    # Build Keyword OR Query Dynamically
    # ---------------------------------------------------------
    def _build_query(self, title: str) -> str:

        tokens = [
            t.strip()
            for t in title.replace(">", " ")
                           .replace("-", " ")
                           .replace("/", " ")
                           .split()
            if len(t.strip()) > 2
        ]

        unique = list(dict.fromkeys(tokens))
        return " OR ".join(unique) if unique else title

    # ---------------------------------------------------------
    # Hybrid Search
    # ---------------------------------------------------------
    def _hybrid_search(self, title: str, channel: str, topk: int = 8):

        search_query = self._build_query(title)

        vector_query = VectorizedQuery(
            vector=self._embed(title),
            fields="embedding",
            k_nearest_neighbors=topk
        )

        filter_query = f"channels/any(c: c eq '{channel}')"

        results = list(
            self.search_client.search(
                search_text=search_query,
                vector_queries=[vector_query],
                filter=filter_query,
                select=["testCaseId", "content"],
                top=topk
            )
        )

        return search_query, results

    # ---------------------------------------------------------
    # Extract Dominant Workflow Pattern (Single LLM Call)
    # ---------------------------------------------------------
    def _extract_workflow_intelligence(self, combined_content: str) -> Dict:

        prompt = f"""
Analyze historical LOS test steps and extract dominant workflow pattern.

Return JSON:

{{
  "precondition": "",
  "workflow_pattern_summary": "",
  "navigation_sequence": [],
  "dominant_step_ordering": []
}}

Content:
{combined_content[:12000]}
"""

        response = self.openai.chat.completions.create(
            model=self.chat_model,
            temperature=0,
            response_format={"type": "json_object"},
            messages=[
                {"role": "system", "content": "You analyze QA workflow patterns."},
                {"role": "user", "content": prompt}
            ]
        )

        try:
            return json.loads(response.choices[0].message.content)
        except Exception:
            return {
                "precondition": "",
                "workflow_pattern_summary": "",
                "navigation_sequence": [],
                "dominant_step_ordering": []
            }

    # ---------------------------------------------------------
    # Save Retrieval Log
    # ---------------------------------------------------------
    def _save_log(self, story_id, channel, search_query, results, workflow_summary):

        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")

        log_file = os.path.join(
            self.log_dir,
            f"retrieval_{story_id}_{channel}_{timestamp}.json"
        )

        log_data = {
            "search_query": search_query,
            "retrieved_count": len(results),
            "retrieved_ids": [doc.get("testCaseId") for doc in results],
            "workflow_summary": workflow_summary
        }

        with open(log_file, "w", encoding="utf-8") as f:
            json.dump(log_data, f, indent=2)

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        title = state["user_story"]
        story_id = state["user_story_id"]

        channel_context = {}

        for channel in state["channels"]:

            search_query, docs = self._hybrid_search(title, channel)

            if not docs:
                continue

            combined_content = "\n\n".join(
                [doc.get("content", "")[:4000] for doc in docs]
            )

            workflow_data = self._extract_workflow_intelligence(combined_content)

            channel_context[channel] = {
                "precondition": workflow_data.get("precondition", ""),
                "workflow_pattern_summary": workflow_data.get("workflow_pattern_summary", ""),
                "navigation_sequence": workflow_data.get("navigation_sequence", []),
                "dominant_step_ordering": workflow_data.get("dominant_step_ordering", [])
            }

            self._save_log(
                story_id,
                channel,
                search_query,
                docs,
                workflow_data
            )

        state["channel_context"] = channel_context
        return state
