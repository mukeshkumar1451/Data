import logging
from typing import Dict, List
from azure.search.documents import SearchClient
from azure.search.documents.models import VectorizedQuery
from azure.core.credentials import AzureKeyCredential
from openai import AzureOpenAI
from config.config import get
import json

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

        logger.info(f"\n🔎 {channel} → Retrieved {len(results)} documents from Vector DB")

        # 🔥 PRINT RAW RETRIEVED DOCUMENTS
        for i, doc in enumerate(results, 1):
            logger.info(f"\n----- RAW DOC {i} -----")
            logger.info(f"TestCaseId: {doc.get('testCaseId')}")
            logger.info(f"Content Preview:\n{doc.get('content')[:800]}")
            logger.info("----------------------")

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

        logger.info("\n📊 RERANKED ORDER:")
        for i, doc in enumerate(ordered_docs, 1):
            logger.info(f"Rank {i}: {doc.get('testCaseId')}")

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

            # 🔥 PRINT STRUCTURED EXTRACTION RESULT
            logger.info("\n🧠 STRUCTURED EXTRACTION RESULT:")
            logger.info(json.dumps(structured, indent=2))

        except Exception as e:
            logger.error(f"Structured extraction failed: {e}")
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

        logger.info("\n🚀 Retrieval Intelligence Agent Running")

        full_story = f"""
User Story: {state['user_story']}
Description: {state['description']}
Acceptance Criteria: {state['acceptance_criteria']}
"""

        logger.info("\n📌 QUERY SENT TO VECTOR SEARCH:")
        logger.info(full_story)

        channel_context = {}

        for channel in state["channels"]:

            logger.info(f"\n==============================")
            logger.info(f"🔵 Processing Channel: {channel}")
            logger.info(f"==============================")

            docs = self._hybrid_search(full_story, channel)

            reranked_docs = self._rerank(full_story, docs)

            best_structured = None

            for doc in reranked_docs:

                content = doc.get("content", "")
                structured = self._extract_structured_content(content)

                if structured.get("precondition") or structured.get("steps"):
                    best_structured = structured
                    break

            if not best_structured:
                logger.warning(f"{channel} → No structured data found.")
                best_structured = {
                    "scenario": "",
                    "script": "",
                    "precondition": "Precondition not found in historical data.",
                    "steps": []
                }

            channel_context[channel] = {
                "precondition": best_structured["precondition"],
                "historical_scenario": best_structured["scenario"],
                "historical_script": best_structured["script"],
                "historical_steps": best_structured["steps"]
            }

        state["channel_context"] = channel_context

        logger.info("\n✅ Retrieval Intelligence Agent Completed")
        return state
