import logging
import os
from typing import Dict, List
from datetime import datetime
import json

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

        # Create retrieval log directory
        self.log_dir = "retrieval_logs"
        os.makedirs(self.log_dir, exist_ok=True)

    # ---------------------------------------------------------
    # Create Log File
    # ---------------------------------------------------------
    def _get_log_file_path(self, story_id: str):
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        return os.path.join(self.log_dir, f"retrieval_{story_id}_{timestamp}.log")

    # ---------------------------------------------------------
    # Embed Story Text
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

        return results

    # ---------------------------------------------------------
    # LLM Rerank
    # ---------------------------------------------------------
    def _rerank(self, story_text: str, docs: List[Dict]) -> List[Dict]:

        if not docs:
            return []

        combined = ""
        for idx, d in enumerate(docs, 1):
            combined += f"\nDoc {idx}:\n{d.get('content')[:2000]}\n"

        prompt = f"""
Rank the below documents by relevance to the story.
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
            structured = json.loads(response.choices[0].message.content)
        except Exception:
            structured = {
                "scenario": "",
                "script": "",
                "precondition": "",
                "steps": []
            }

        return structured

    # ---------------------------------------------------------
    # Main Execution
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        story_id = state.get("user_story_id", "unknown")
        log_file_path = self._get_log_file_path(story_id)

        with open(log_file_path, "w", encoding="utf-8") as log_file:

            log_file.write("===== RETRIEVAL DEBUG LOG =====\n\n")

            full_story = f"""
User Story: {state['user_story']}
Description: {state['description']}
Acceptance Criteria: {state['acceptance_criteria']}
"""

            log_file.write("----- QUERY SENT TO VECTOR DB -----\n")
            log_file.write(full_story + "\n\n")

            channel_context = {}

            for channel in state["channels"]:

                log_file.write("\n====================================\n")
                log_file.write(f"CHANNEL: {channel}\n")
                log_file.write("====================================\n\n")

                # 1️⃣ Hybrid Search
                docs = self._hybrid_search(full_story, channel)

                log_file.write(f"Retrieved {len(docs)} documents\n\n")

                for i, doc in enumerate(docs, 1):
                    log_file.write(f"\n--- RAW DOC {i} ---\n")
                    log_file.write(f"TestCaseId: {doc.get('testCaseId')}\n")
                    log_file.write("Content Preview:\n")
                    log_file.write(doc.get("content", "")[:2000] + "\n")

                # 2️⃣ Rerank
                reranked_docs = self._rerank(full_story, docs)

                log_file.write("\n--- RERANKED ORDER ---\n")
                for i, doc in enumerate(reranked_docs, 1):
                    log_file.write(f"Rank {i}: {doc.get('testCaseId')}\n")

                best_structured = None

                # 3️⃣ Structured Extraction
                for doc in reranked_docs:

                    content = doc.get("content", "")
                    structured = self._extract_structured_content(content)

                    log_file.write("\n--- STRUCTURED EXTRACTION ---\n")
                    log_file.write(json.dumps(structured, indent=2))
                    log_file.write("\n")

                    if structured.get("precondition") or structured.get("steps"):
                        best_structured = structured
                        break

                # 4️⃣ Fallback
                if not best_structured:
                    log_file.write("\nNo structured content found. Using fallback.\n")
                    best_structured = {
                        "scenario": "",
                        "script": "",
                        "precondition": "Precondition not found in historical data.",
                        "steps": []
                    }

                log_file.write("\n--- FINAL SELECTED STRUCTURED DOC ---\n")
                log_file.write(json.dumps(best_structured, indent=2))
                log_file.write("\n")

                channel_context[channel] = {
                    "precondition": best_structured["precondition"],
                    "historical_scenario": best_structured["scenario"],
                    "historical_script": best_structured["script"],
                    "historical_steps": best_structured["steps"]
                }

            log_file.write("\n===== RETRIEVAL COMPLETED =====\n")

        print(f"\nRetrieval debug log saved at: {log_file_path}")

        state["channel_context"] = channel_context
        return state
