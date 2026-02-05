# rag_query.py

from azure.search.documents import SearchClient
from azure.search.documents.models import VectorQuery
from azure.core.credentials import AzureKeyCredential
from openai import AzureOpenAI
from config import get


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
    # Detect channel from Acceptance Criteria
    # ----------------------------------------------------
    def detect_channels(self, ac_text):
        ac = ac_text.upper()

        channels = []

        if "WHOLESALE" in ac or "WHL" in ac:
            channels.append("WHL")
        if "RETAIL" in ac or "RTL" in ac:
            channels.append("RTL")
        if "DTC" in ac or "DIRECT TO CUSTOMER" in ac:
            channels.append("DTC")
        if "CORRESPONDENT" in ac or "CL1" in ac:
            channels.append("CL1")

        # If no channel mentioned → all
        if not channels:
            channels = ["RTL", "WHL", "DTC", "CL1"]

        print(f"🔎 Channels from AC: {channels}")
        return channels

    # ----------------------------------------------------
    # Create embedding for user story
    # ----------------------------------------------------
    def embed_query(self, text):
        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=text
        )
        return emb.data[0].embedding

    # ----------------------------------------------------
    # Retrieve similar chunks from vector DB
    # ----------------------------------------------------
    def retrieve(self, user_story, description, ac):

        query_text = f"{user_story}\n{description}\n{ac}"
        query_vector = self.embed_query(query_text)

        channels = self.detect_channels(ac)
        filter_query = " or ".join([f"channel eq '{c}'" for c in channels])

        vector_query = VectorQuery(
            kind="vector",
            fields="embedding",
            k_nearest_neighbors=self.top_k,
            value=query_vector
        )

        results = self.search_client.search(
            search_text=None,
            vector_queries=[vector_query],
            filter=filter_query,
            select=["testCaseId", "chunkId", "content", "channel"]
        )

        return list(results)

    # ----------------------------------------------------
    # Rebuild full testcase from chunks
    # ----------------------------------------------------
    def rebuild_testcase(self, testcase_id):

        results = self.search_client.search(
            search_text="*",
            filter=f"testCaseId eq '{testcase_id}'",
            select=["chunkId", "content"],
            top=50
        )

        chunks = sorted(results, key=lambda x: x["chunkId"])

        full_text = "\n".join([c["content"] for c in chunks])
        return full_text
