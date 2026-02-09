from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from azure.search.documents.models import VectorizedQuery

import logging
from openai import AzureOpenAI
from embeddingtovectordb.config import get
from ContextRetrieval_ReRanking.prompts.prompt_templates import build_testcase_prompt
from ContextRetrieval_ReRanking.rerankerbase.reranker import LLMReranker

logger = logging.getLogger(__name__)


class TestCaseRAGRetriever:

    def __init__(self):

        # -------- Azure OpenAI --------
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION")
        )

        self.chat_model = get("CHAT_MODEL")
        self.embed_model = get("EMBEDDING_MODEL")
        self.top_k = get("TOP_K", int)

        # -------- LLM Reranker --------
        self.reranker = LLMReranker(self.openai, self.chat_model)

        # -------- Azure AI Search --------
        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
        )

    # ----------------------------------------------------
    # Create embedding
    # ----------------------------------------------------
    def embed_query(self, text):
        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=text
        )
        return emb.data[0].embedding

    # ----------------------------------------------------
    # Hybrid Search + Re-ranking for ONE channel
    # ----------------------------------------------------
    def retrieve_for_channel(self, user_story, description, ac, channel):
        logger.info(f"\n🔎 Hybrid search for channel: {channel}")

        query_text = f"""
User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}
"""

        query_vector = self.embed_query(query_text)

        vector_query = VectorizedQuery(
            kind="vector",
            vector=query_vector,
            k=self.top_k,
            fields="embedding"
        )

        # ✅ ONLY VALID FILTER FOR YOUR INDEX
        results = self.search_client.search(
            search_text=query_text,
            vector_queries=[vector_query],
            filter=f"channels/any(c: c eq '{channel}')",
            select=["id", "testCaseId", "channels", "content"],
            top=self.top_k
        )

        results_list = list(results)
        logger.info(f"✅ Retrieved {len(results_list)} testcases before re-ranking")

        # ---------------- Re-ranking ----------------
        reranked = self.reranker.rerank(query_text, results_list)

        logger.info("\n🔁 After LLM Re-ranking:\n")
        for r in reranked[:10]:
            logger.info(
                f"   🦬 Rerank: {r['rerank_score']:.3f} | "
                f"TC: {r['testCaseId']}"
            )

        return reranked

    # ----------------------------------------------------
    # Build historical context
    # ----------------------------------------------------
    def _build_historical_context(self, results):
        historical_context = ""

        for r in results:
            tcid = r["testCaseId"]
            content = r["content"]

            historical_context += (
                f"\n\n### Historical TestCase: {tcid}\n{content}\n"
            )

        return historical_context

    # ----------------------------------------------------
    # Generate testcase using LLM
    # ----------------------------------------------------
    def generate_testcase_with_llm(
        self,
        user_story_id,
        user_story,
        description,
        ac,
        retrieved_chunks,
    ):

        if not retrieved_chunks:
            logger.warning("⚠️ No historical testcases → skipping LLM")
            return {}

        # channels is a LIST
        channel = retrieved_chunks[0]["channels"][0]

        historical_context = self._build_historical_context(retrieved_chunks)

        prompt = build_testcase_prompt(
            user_story_id=user_story_id,
            user_story=user_story,
            description=description,
            ac=ac,
            historical_context=historical_context
        )

        logger.info(f"🤖 Sending {channel} context to Azure OpenAI...\n")

        response = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[
                {"role": "system", "content": "You are a QA Test Case Designer."},
                {"role": "user", "content": prompt}
            ],
            temperature=0.2
        )

        output = response.choices[0].message.content
        logger.info(f"✅ LLM response received for {channel}\n")

        return {channel: output}
