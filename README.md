import logging
from typing import Dict, List

from azure.search.documents import SearchClient
from azure.search.documents.models import VectorizedQuery
from azure.core.credentials import AzureKeyCredential
from openai import AzureOpenAI

from config.config import get

logger = logging.getLogger(__name__)


class RetrievalAgent:

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

    # -------------------------------------------------
    # Embed Query
    # -------------------------------------------------
    def _embed(self, text: str):

        response = self.openai.embeddings.create(
            model=self.embed_model,
            input=text[:8000]
        )

        return response.data[0].embedding

    # -------------------------------------------------
    # Hybrid Search
    # -------------------------------------------------
    def _hybrid_search(self, query: str, channel: str, topk: int = 20):

        vector_query = VectorizedQuery(
            vector=self._embed(query),
            fields="embedding",
            k_nearest_neighbors=topk
        )

        results = list(self.search_client.search(
            search_text=query,
            vector_queries=[vector_query],
            filter=f"channels/any(c: c eq '{channel}')",
            select=["testCaseId", "content"],
            top=topk
        ))

        return results

    # -------------------------------------------------
    # Extract Precondition + Steps
    # -------------------------------------------------
    def _extract_context(self, content: str):

        parts = content.split("=========== TEST STEPS ===========")

        precondition = parts[0].strip()
        steps = parts[1].strip() if len(parts) > 1 else ""

        return precondition, steps

    # -------------------------------------------------
    # Rerank using LLM
    # -------------------------------------------------
    def _rerank(self, query: str, docs: List[Dict]):

        combined = ""

        for idx, d in enumerate(docs, start=1):
            combined += f"""
Document {idx}
TestCaseId: {d.get('testCaseId')}
Content:
{d.get('content')[:1500]}
---------------------
"""

        prompt = f"""
You are a QA analyst.

Rank the testcases by relevance to this workflow.

Workflow:
{query}

Return ONLY ordered TestCaseId values.
"""

        resp = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[{"role": "user", "content": prompt + combined}],
            temperature=0
        )

        ranked_ids = resp.choices[0].message.content.strip().splitlines()

        id_map = {d["testCaseId"]: d for d in docs}

        ordered = [id_map[x] for x in ranked_ids if x in id_map]

        return ordered[:5] if ordered else docs[:5]

    # -------------------------------------------------
    # Main Entry
    # -------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 Retrieval Agent Running")

        full_query = f"""
User Story: {state['user_story']}
Description: {state['description']}
Acceptance Criteria: {state['acceptance_criteria']}
"""

        channel_context = {}

        for channel in state["channels"]:

            raw_docs = self._hybrid_search(full_query, channel, 30)

            reranked = self._rerank(full_query, raw_docs)

            structured = []

            for d in reranked:
                pre, steps = self._extract_context(d["content"])

                structured.append({
                    "testCaseId": d["testCaseId"],
                    "precondition": pre,
                    "steps": steps
                })

            channel_context[channel] = structured

        state["channel_context"] = channel_context

        logger.info("✅ Retrieval Completed")

        return state
  ============================================================================
  import logging
from typing import Dict

from langchain_openai import AzureChatOpenAI
from langchain_core.prompts import PromptTemplate
from config.config import get

logger = logging.getLogger(__name__)


class LLMGeneratorAgent:

    def __init__(self):

        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0.2,
        )

        self.prompt = PromptTemplate(
            input_variables=[
                "user_story",
                "description",
                "ac",
                "preconditions",
                "historical_tests"
            ],
            template="""
You are a QA automation expert.

User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}

Relevant Preconditions:
{preconditions}

Historical Testcases:
{historical_tests}

Generate a NEW structured testcase.
Use preconditions as setup context.
Do not copy historical steps directly.
Return structured steps.
"""
        )

        self.chain = self.prompt | self.llm

    def run(self, state: Dict) -> Dict:

        logger.info("🤖 LLM Generator Running")

        outputs = {}

        for channel, contexts in state["channel_context"].items():

            preconditions = "\n\n".join(
                [c["precondition"] for c in contexts]
            )

            historical_tests = "\n\n".join(
                [c["steps"][:1200] for c in contexts]
            )

            payload = {
                "user_story": state["user_story"],
                "description": state["description"],
                "ac": state["acceptance_criteria"],
                "preconditions": preconditions,
                "historical_tests": historical_tests
            }

            result = self.chain.invoke(payload)

            outputs[channel] = result.content

            print("\n===== GENERATED OUTPUT =====\n")
            print(result.content)

        state["llm_outputs"] = outputs

        logger.info("✅ LLM Generation Completed")

        return state
        
