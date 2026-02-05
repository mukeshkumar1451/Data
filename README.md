from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from openai import AzureOpenAI
from config import get
from channel_detector import detect_channels


class TestCaseRAGRetriever:

    def __init__(self):
        # -------- Azure Search --------
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

        self.embed_model = get("EMBEDDING_MODEL_DEPLOYMENT")
        self.top_k = get("TOP_K", int)

    # ----------------------------------------------------
    # Create embedding
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
    # Retrieve similar chunks from vector DB
    # ----------------------------------------------------
    def retrieve(self, user_story, description, ac):

        print("\n🔹 Step 1: Detecting channels from AC")
        channels = detect_channels(ac)

        filter_query = " or ".join([f"channel eq '{c}'" for c in channels])
        print(f"🔎 Channel Filter: {filter_query}")

        print("\n🔹 Step 2: Preparing semantic query text")
        query_text = f"""
        User Story:
        {user_story}

        Description:
        {description}

        Acceptance Criteria:
        {ac}
        """

        query_vector = self.embed_query(query_text)

        print("\n🔹 Step 3: Sending vector search to Azure AI Search")

        vector_query = {
            "kind": "vector",
            "vector": query_vector,
            "fields": "embedding",
            "k": self.top_k
        }

        results = self.search_client.search(
            search_text=None,
            vector=vector_query,
            filter=filter_query,
            select=["testCaseId", "chunkId", "content", "channel"]
        )

        results_list = list(results)
        print(f"✅ Retrieved {len(results_list)} chunks from vector DB\n")

        return results_list

    # ----------------------------------------------------
    # Rebuild full testcase from chunks
    # ----------------------------------------------------
    def rebuild_testcase(self, testcase_id):

        print(f"🧩 Rebuilding full testcase for: {testcase_id}")

        results = self.search_client.search(
            search_text="*",
            filter=f"testCaseId eq '{testcase_id}'",
            select=["chunkId", "content"],
            top=50
        )

        chunks = sorted(results, key=lambda x: x["chunkId"])

        full_text = "\n".join([c["content"] for c in chunks])

        return full_text
