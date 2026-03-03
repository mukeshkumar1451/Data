import logging
import json
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
    # Embed Story
    # ---------------------------------------------------------
    def _embed(self, text: str) -> List[float]:
        response = self.openai.embeddings.create(
            model=self.embed_model,
            input=text[:8000]
        )
        return response.data[0].embedding

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

        results = list(
            self.search_client.search(
                search_text=query_text,
                vector_queries=[vector_query],
                filter=filter_query,
                select=["testCaseId", "content"],
                top=topk
            )
        )

        logger.info(f"{channel} → Retrieved {len(results)} documents")
        return results

    # ---------------------------------------------------------
    # Rerank Documents
    # ---------------------------------------------------------
    def _rerank(self, story_text: str, docs: List[Dict]) -> List[Dict]:

        if not docs:
            return []

        combined = ""
        for idx, d in enumerate(docs, 1):
            combined += f"\nDoc {idx}:\n{d.get('content')[:2000]}\n"

        prompt = f"""
Rank documents based on:
- Matching screen names
- Matching field names
- Matching UI control types (dropdown, checkbox, picker)
- Matching privilege or audit behavior
- Matching workflow navigation

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
    # Structured Extraction
    # ---------------------------------------------------------
    def _extract_structured_content(self, content: str) -> Dict:

        extraction_prompt = f"""
Extract structured JSON strictly in this format:

{{
  "scenario": "",
  "script": "",
  "precondition": "",
  "steps": [
    {{
      "stepNo": "",
      "description": "",
      "expectedResult": ""
    }}
  ]
}}

Return JSON only.

Content:
{content[:6000]}
"""

        response = self.openai.chat.completions.create(
            model=self.chat_model,
            temperature=0,
            response_format={"type": "json_object"},
            messages=[
                {"role": "system", "content": "You are a QA test case extraction engine."},
                {"role": "user", "content": extraction_prompt}
            ]
        )

        try:
            return json.loads(response.choices[0].message.content)
        except Exception:
            return {"scenario": "", "script": "", "precondition": "", "steps": []}

    # ---------------------------------------------------------
    # Main Execution
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Retrieval Intelligence Agent Running")

        full_story = f"""
User Story: {state['user_story']}
Description: {state['description']}
Acceptance Criteria: {state['acceptance_criteria']}
"""

        channel_context = {}

        for channel in state["channels"]:

            logger.info(f"Processing channel: {channel}")

            docs = self._hybrid_search(full_story, channel)
            reranked_docs = self._rerank(full_story, docs)

            aggregated = {
                "scenario_samples": [],
                "script_samples": [],
                "preconditions": [],
                "step_patterns": []
            }

            top_docs = reranked_docs[:5]

            for doc in top_docs:
                content = doc.get("content", "")
                structured = self._extract_structured_content(content)

                if structured.get("scenario"):
                    aggregated["scenario_samples"].append(structured["scenario"])

                if structured.get("script"):
                    aggregated["script_samples"].append(structured["script"])

                if structured.get("precondition"):
                    aggregated["preconditions"].append(structured["precondition"])

                if structured.get("steps"):
                    aggregated["step_patterns"].extend(structured["steps"])

            # Save retrieval log
            with open(f"retrieval_log_{channel}.json", "w", encoding="utf-8") as f:
                json.dump(aggregated, f, indent=4)

            channel_context[channel] = {
                "precondition": aggregated["preconditions"][0] if aggregated["preconditions"] else "",
                "historical_scenarios": aggregated["scenario_samples"][:3],
                "historical_scripts": aggregated["script_samples"][:3],
                "historical_steps": aggregated["step_patterns"][:20]
            }

        state["channel_context"] = channel_context

        logger.info("Retrieval Intelligence Agent Completed")
        return state
