from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from azure.search.documents.models import VectorizedQuery

from openai import AzureOpenAI

from embeddingtovectordb.config import get
from prompt_templates import build_testcase_prompt


class TestCaseRAGRetriever:

    def __init__(self):

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
    # Create embedding for semantic query
    # ----------------------------------------------------
    def embed_query(self, text):
        print("🧠 Creating embedding from User Story + Description + AC...")

        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=text
        )

        vec = emb.data[0].embedding
        print(f"✅ Embedding length: {len(vec)}")
        return vec

    # ----------------------------------------------------
    # Vector search ONLY for a specific channel
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

        vector_query = VectorizedQuery(
            kind="vector",
            vector=query_vector,
            k_nearest_neighbors=self.top_k,
            fields="embedding"
        )

        results = self.search_client.search(
            search_text=None,
            vector_queries=[vector_query],
            filter=f"channel eq '{channel}'",
            select=["testCaseId", "chunkId", "content", "channel"]
        )

        results_list = list(results)
        print(f"✅ Retrieved {len(results_list)} chunks for {channel}")

        return results_list

    # ----------------------------------------------------
    # Rebuild historical testcase text from chunks
    # ----------------------------------------------------
    def _build_historical_context(self, retrieved_chunks):

        print("🧩 Rebuilding historical testcases from chunks...")

        tc_map = {}

        for r in retrieved_chunks:
            tcid = r["testCaseId"]
            chunk_id = int(r["chunkId"])
            content = r["content"]

            tc_map.setdefault(tcid, []).append((chunk_id, content))

        historical_context = ""

        for tcid, chunks in tc_map.items():
            print(f"   ↳ Rebuilding TestCase: {tcid}")

            chunks_sorted = sorted(chunks, key=lambda x: x[0])
            full_text = "\n".join([c[1] for c in chunks_sorted])

            historical_context += f"\n\n### Historical TestCase: {tcid}\n{full_text}\n"

        print("✅ Historical context ready\n")
        return historical_context

    # ----------------------------------------------------
    # TRUE RAG — Generate testcase using channel context
    # ----------------------------------------------------
    def generate_testcase_with_llm(
        self,
        user_story_id,
        user_story,
        description,
        ac,
        retrieved_chunks,
        channel
    ):

        historical_context = self._build_historical_context(retrieved_chunks)

        prompt = build_testcase_prompt(
            user_story_id=user_story_id,
            user_story=user_story,
            description=description,
            ac=ac,
            historical_context=historical_context,
            channel=channel
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
        print("✅ LLM Response Received\n")

        return output
