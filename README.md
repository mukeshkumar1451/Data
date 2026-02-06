from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from azure.search.documents.models import VectorizedQuery

from openai import AzureOpenAI

from embeddingtovectordb.config import get
from prompts.prompt_templates import build_testcase_prompt
from rerankerbase.reranker import LLMReranker


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

        # -------- Reranker --------
        self.reranker = LLMReranker(self.openai, self.chat_model)

        # -------- Azure AI Search --------
        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
        )

    # ----------------------------------------------------
    def embed_query(self, text):
        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=text
        )
        return emb.data[0].embedding

    # ----------------------------------------------------
    def retrieve_for_channel(self, user_story, description, ac, channel):

        print(f"\n🔎 Vector search for channel: {channel}")

        query_text = f"""
User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}
"""

        query_vector = self.embed_query(query_text)

        # ✅ CORRECT for SDK
        vector_query = VectorizedQuery(
            vector=query_vector,
            fields="embedding",
            k_nearest_neighbors=self.top_k
        )

        results = self.search_client.search(
            search_text=None,
            vector_queries=[vector_query],
            filter=f"channel eq '{channel}'",
            select=["testCaseId", "chunkId", "content", "channel"]
        )

        results_list = list(results)
        print(f"✅ Retrieved {len(results_list)} chunks before re-ranking")

        if not results_list:
            return []

        # ---------------- Re-ranking ----------------
        reranked = self.reranker.rerank(
            query_text=query_text,
            results=results_list,
            threshold=0.5,
            top_n=12
        )

        if not reranked:
            print("⚠️ No chunks passed re-ranking → using top vector results")
            reranked = results_list[:12]

        print(f"✅ {len(reranked)} chunks after re-ranking")
        return reranked

    # ----------------------------------------------------
    def _build_historical_context(self, chunks):

        tc_map = {}

        for r in chunks:
            tcid = r["testCaseId"]
            chunk_id = int(r["chunkId"])
            content = r["content"]
            tc_map.setdefault(tcid, []).append((chunk_id, content))

        historical_context = ""

        for tcid, parts in tc_map.items():
            parts_sorted = sorted(parts, key=lambda x: x[0])
            full_text = "\n".join([p[1] for p in parts_sorted])
            historical_context += f"\n\n### Historical TestCase: {tcid}\n{full_text}\n"

        return historical_context

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
            print("⚠️ No chunks → Skipping LLM generation for this channel")
            return {}

        channel = retrieved_chunks[0]["channel"]

        historical_context = self._build_historical_context(retrieved_chunks)

        prompt = build_testcase_prompt(
            user_story_id=user_story_id,
            user_story=user_story,
            description=description,
            ac=ac,
            historical_context=historical_context
        )

        print(f"🤖 Sending {channel} context to Azure OpenAI...\n")

        response = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[
                {"role": "system", "content": "You are a QA Test Case Designer."},
                {"role": "user", "content": prompt}
            ],
            temperature=0.2
        )

        output = response.choices[0].message.content
        print(f"✅ LLM response received for {channel}\n")

        return {channel: output}
