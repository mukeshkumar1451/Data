from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from azure.search.documents.models import VectorizedQuery

from openai import AzureOpenAI

from embeddingtovectordb.config import get
from prompts.prompt_templates import build_testcase_prompt
from rerankerbase.reranker import CrossEncoderReranker


class TestCaseRAGRetriever:

    def __init__(self):

        self.reranker = CrossEncoderReranker()

        # -------- Azure AI Search --------
        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
        )

        # -------- Azure OpenAI --------
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION")
        )

        self.embed_model = get("EMBEDDING_MODEL")
        self.chat_model = get("CHAT_MODEL")
        self.top_k = get("TOP_K", int)

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
    # Vector search for ONE channel + rerank
    # ----------------------------------------------------
    def retrieve_for_channel(self, user_story, description, ac, channel):

        filter_query = f"channel eq '{channel}'"

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
            k_nearest_neighbors=self.top_k,
            fields="embedding"
        )

        results = self.search_client.search(
            search_text=None,
            vector_queries=[vector_query],
            filter=filter_query,
            select=["testCaseId", "chunkId", "content", "channel"]
        )

        results_list = list(results)

        # 🔥 Cross Encoder Re-ranking
        reranked = self.reranker.rerank(
            query_text=query_text,
            search_results=results_list,
            threshold=0.5,
            top_n=12
        )

        return reranked

    # ----------------------------------------------------
    # Group chunks by channel  ✅ FIXED
    # ----------------------------------------------------
    def group_by_channel(self, reranked_chunks):
        channel_map = {}

        for r in reranked_chunks:
            ch = r["channel"]
            channel_map.setdefault(ch, []).append(r)

        return channel_map

    # ----------------------------------------------------
    # Build historical context
    # ----------------------------------------------------
    def _build_historical_context(self, chunks):

        tc_map = {}

        for r in chunks:
            tcid = r["testCaseId"]
            chunk_id = int(r["chunkId"])
            content = r["content"]

            tc_map.setdefault(tcid, []).append((chunk_id, content))

        historical_context = ""

        for tcid, cks in tc_map.items():
            sorted_chunks = sorted(cks, key=lambda x: x[0])
            full_text = "\n".join([c[1] for c in sorted_chunks])

            historical_context += f"\n\n### Historical TestCase: {tcid}\n{full_text}\n"

        return historical_context

    # ----------------------------------------------------
    # TRUE Channel-aware RAG
    # ----------------------------------------------------
    def generate_testcase_with_llm(
        self,
        user_story_id,
        user_story,
        description,
        ac,
        retrieved_chunks,
    ):

        channel_group = self.group_by_channel(retrieved_chunks)
        final_outputs = {}

        for channel, chunks in channel_group.items():

            print(f"\n=== Generating TestCase for channel: {channel} ===")

            historical_context = self._build_historical_context(chunks)

            prompt = build_testcase_prompt(
                user_story_id=user_story_id,
                user_story=user_story,
                description=description,
                ac=ac,
                historical_context=historical_context
            )

            response = self.openai.chat.completions.create(
                model=self.chat_model,
                messages=[
                    {"role": "system", "content": "You are a QA Test Case Designer."},
                    {"role": "user", "content": prompt}
                ],
                temperature=0.2
            )

            final_outputs[channel] = response.choices[0].message.content
            print("✅ LLM Response Received")

        return final_outputs
