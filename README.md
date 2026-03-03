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

        # Log directory
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
    # Build Dynamic Keyword OR Query (No Hardcoding)
    # ---------------------------------------------------------
    def _build_keyword_query(self, title: str) -> str:

        tokens = [
            word.strip()
            for word in title.replace(">", " ")
                             .replace("-", " ")
                             .replace("/", " ")
                             .split()
            if len(word.strip()) > 2
        ]

        # Remove duplicates dynamically
        unique_tokens = list(dict.fromkeys(tokens))

        if not unique_tokens:
            return title

        return " OR ".join(unique_tokens)

    # ---------------------------------------------------------
    # Hybrid Search (Vector + Keyword)
    # ---------------------------------------------------------
    def _hybrid_search(self, title: str, channel: str, topk: int = 10):

        search_query = self._build_keyword_query(title)

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
    # Rule-Based Flow Pattern Detection (Generic)
    # ---------------------------------------------------------
    def _detect_flow_patterns(self, text: str) -> Dict:

        lower = text.lower()

        return {
            "audit_behavioral_pattern":
                lower.count("audit") > 1 and
                lower.count("previous") > 0 and
                lower.count("new") > 0,

            "requires_save_cycle":
                lower.count("save") > 0,

            "value_transition_pattern":
                lower.count("yes") > 0 and lower.count("no") > 0,

            "has_checkbox_pattern":
                lower.count("checkbox") > 0,

            "has_dropdown_pattern":
                lower.count("dropdown") > 0
        }

    # ---------------------------------------------------------
    # Single LLM Extraction (Structure Only)
    # ---------------------------------------------------------
    def _extract_summary(self, combined_content: str) -> Dict:

        prompt = f"""
Extract structured JSON:

{{
  "precondition": "",
  "historical_scenarios": [],
  "historical_scripts": [],
  "step_patterns": []
}}

Return JSON only.

Content:
{combined_content[:12000]}
"""

        response = self.openai.chat.completions.create(
            model=self.chat_model,
            temperature=0,
            response_format={"type": "json_object"},
            messages=[
                {"role": "system", "content": "You extract QA structural patterns."},
                {"role": "user", "content": prompt}
            ]
        )

        try:
            return json.loads(response.choices[0].message.content)
        except Exception:
            return {
                "precondition": "",
                "historical_scenarios": [],
                "historical_scripts": [],
                "step_patterns": []
            }

    # ---------------------------------------------------------
    # Save Retrieval Log (Full Transparency)
    # ---------------------------------------------------------
    def _save_retrieval_log(
        self,
        story_id: str,
        channel: str,
        title: str,
        search_query: str,
        results: List[Dict],
        flow_intelligence: Dict
    ):

        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")

        log_file = os.path.join(
            self.log_dir,
            f"retrieval_{story_id}_{channel}_{timestamp}.json"
        )

        log_data = {
            "story_id": story_id,
            "channel": channel,
            "title_used_for_search": title,
            "search_query_used": search_query,
            "retrieved_count": len(results),
            "retrieved_documents": [
                {
                    "testCaseId": doc.get("testCaseId"),
                    "content_preview": doc.get("content", "")[:2000]
                }
                for doc in results
            ],
            "flow_intelligence": flow_intelligence
        }

        with open(log_file, "w", encoding="utf-8") as f:
            json.dump(log_data, f, indent=2)

        logger.info(f"Retrieval log saved: {log_file}")

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Retrieval Intelligence Agent Running")

        title = state["user_story"]
        story_id = state["user_story_id"]

        channel_context = {}

        for channel in state["channels"]:

            logger.info(f"Processing channel: {channel}")

            search_query, docs = self._hybrid_search(title, channel)

            if not docs:
                logger.warning(f"No documents retrieved for {channel}")
                continue

            combined_content = "\n\n".join(
                [doc.get("content", "")[:4000] for doc in docs]
            )

            flow_intelligence = self._detect_flow_patterns(combined_content)

            structured = self._extract_summary(combined_content)

            channel_context[channel] = {
                "precondition": structured.get("precondition", ""),
                "historical_scenarios": structured.get("historical_scenarios", [])[:3],
                "historical_scripts": structured.get("historical_scripts", [])[:3],
                "historical_steps": structured.get("step_patterns", [])[:30],
                "flow_intelligence": flow_intelligence
            }

            # Save detailed retrieval log
            self._save_retrieval_log(
                story_id,
                channel,
                title,
                search_query,
                docs,
                flow_intelligence
            )

        state["channel_context"] = channel_context

        logger.info("Retrieval Intelligence Agent Completed")
        return state
