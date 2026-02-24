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

    # -------------------------------------------------
    # Embed query
    # -------------------------------------------------
    def _embed(self, text: str) -> List[float]:

        response = self.openai.embeddings.create(
            model=self.embed_model,
            input=text[:8000]
        )

        return response.data[0].embedding

    # -------------------------------------------------
    # Hybrid Search (Vector + Keyword)
    # -------------------------------------------------
    def _hybrid_search(self, query_text: str, channel: str, topk: int = 30):

        logger.info(f"🔎 Hybrid search for channel: {channel}")

        vector_query = VectorizedQuery(
            vector=self._embed(query_text),
            fields="embedding",
            k_nearest_neighbors=topk
        )

        results = list(self.search_client.search(
            search_text=query_text,
            vector_queries=[vector_query],
            filter=f"channels/any(c: c eq '{channel}')",
            select=["testCaseId", "content"],
            top=topk
        ))

        logger.info(f"📊 Retrieved {len(results)} docs for {channel}")
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
    # LLM Rerank
    # -------------------------------------------------
    def _rerank(self, query_text: str, docs: List[Dict]):

        if not docs:
            return []

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
{query_text}

Return ONLY ordered TestCaseId values.
"""

        response = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[{"role": "user", "content": prompt + combined}],
            temperature=0
        )

        ranked_ids = response.choices[0].message.content.strip().splitlines()

        id_map = {d["testCaseId"]: d for d in docs}
        ordered = [id_map[x] for x in ranked_ids if x in id_map]

        return ordered[:5] if ordered else docs[:5]

    # -------------------------------------------------
    # Main Entry
    # -------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 Retrieval Intelligence Agent Running")

        full_query = f"""
User Story: {state['user_story']}
Description: {state['description']}
Acceptance Criteria: {state['acceptance_criteria']}
"""

        channel_context = {}

        for channel in state["channels"]:

            raw_docs = self._hybrid_search(full_query, channel)

            reranked = self._rerank(full_query, raw_docs)

            structured = []

            for d in reranked:
                pre, steps = self._extract_context(d["content"])

                structured.append({
                    "testCaseId": d["testCaseId"],
                    "retrieved_precondition": pre,
                    "retrieved_steps": steps
                })

            channel_context[channel] = structured

        state["channel_context"] = channel_context

        logger.info("✅ Retrieval Completed")
        return state
=======================================================================================
import logging
from typing import Dict

from langchain_openai import AzureChatOpenAI
from langchain_core.prompts import PromptTemplate
from config.config import get

logger = logging.getLogger(__name__)


class LLMTestcaseGeneratorAgent:

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
                "historical_tests",
                "channel"
            ],
            template="""
You are a QA automation expert.

User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}

Channel:
{channel}

Historical Testcases:
{historical_tests}

IMPORTANT:

You MUST generate precondition using EXACT template below.
Do NOT change wording.
Do NOT add extra bullets.

If Channel = RTL:

Create a loan from Customer Portal as per pre-conditions below:
1. Channel: RTL
2. Loan Purpose:
3. Loan Type:
4. Product Code:
5. Loan Stage:

If Channel = WHL:

Create a loan from Broker Portal as per pre-conditions below:
1. Channel: Wholesale
2. Loan Purpose:
3. Loan Type:
4. Product Code:
5. Loan Stage:

If Channel = DTC:

Create a loan from Ignite Portal as per pre-conditions below:
1. Channel: DTC
2. Loan Purpose:
3. Loan Type:
4. Product Code:
5. Loan Stage:

If Channel = CL1:

Create a loan from Broker Portal as per pre-conditions below:
1. Channel: CL1
2. Loan Purpose:
3. Loan Type:
4. Product Code:
5. Loan Stage:

After precondition, generate structured test steps.
"""
        )

        self.chain = self.prompt | self.llm

    # -------------------------------------------------
    # Build historical context
    # -------------------------------------------------
    def _build_historical_text(self, contexts):

        text = ""

        for c in contexts:
            text += "\n--- Historical Testcase ---\n"
            text += c["retrieved_steps"][:1000]

        return text[:6000]

    # -------------------------------------------------
    # Main Entry
    # -------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("🤖 LLM Generator Running")

        outputs = {}

        for channel, contexts in state["channel_context"].items():

            historical_text = self._build_historical_text(contexts)

            payload = {
                "user_story": state["user_story"],
                "description": state["description"],
                "ac": state["acceptance_criteria"],
                "historical_tests": historical_text,
                "channel": channel
            }

            result = self.chain.invoke(payload)

            outputs[channel] = result.content

            print("\n===== GENERATED OUTPUT =====\n")
            print(result.content)

        state["llm_outputs"] = outputs

        logger.info("✅ LLM Generation Completed")
        return state
        
