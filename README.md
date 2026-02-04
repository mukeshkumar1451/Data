# rag_query.py

from openai import AzureOpenAI
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from config import get


class TestCaseRAGRetriever:

    def __init__(self):
        # Azure OpenAI client
        self.openai_client = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT")
        )

        # Azure AI Search client
        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
        )

        self.top_k = get("TOP_K", int)
        self.embedding_model = get("EMBEDDING_MODEL")

    # ---------------------------------------------------
    # Build semantic query from user inputs
    # ---------------------------------------------------
    def _build_query(self, user_story, description, ac):
        return f"""
User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}

Find the most relevant test cases for this functionality.
"""

    # ---------------------------------------------------
    # Create embedding for the query
    # ---------------------------------------------------
    def _embed_query(self, text):
        emb = self.openai_client.embeddings.create(
            model=self.embedding_model,
            input=text
        )
        return emb.data[0].embedding

    # ---------------------------------------------------
    # Run vector search in Azure AI Search
    # ---------------------------------------------------
    def retrieve(self, user_story, description, ac, channel_filter=None):
        query_text = self._build_query(user_story, description, ac)
        query_vector = self._embed_query(query_text)

        vector_query = {
            "vector": query_vector,
            "k": self.top_k,
            "fields": "embedding"
        }

        filter_expr = None
        if channel_filter:
            filter_expr = f"channel eq '{channel_filter}'"

        results = self.search_client.search(
            search_text=None,
            vector_queries=[vector_query],
            filter=filter_expr
        )

        matches = []
        for r in results:
            matches.append({
                "testCaseId": r["testCaseId"],
                "chunkId": r["chunkId"],
                "channel": r["channel"],
                "score": r["@search.score"],
                "content": r["content"]
            })

        return matches

    # ---------------------------------------------------
    # Reassemble full testcase from chunks
    # ---------------------------------------------------
    def rebuild_testcase(self, test_case_id):
        results = self.search_client.search(
            search_text="*",
            filter=f"testCaseId eq '{test_case_id}'",
            order_by="chunkId asc"
        )

        full_text = ""
        for r in results:
            full_text += r["content"] + "\n"

        return full_text
