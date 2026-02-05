from openai import AzureOpenAI
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from config import get


CHANNEL_KEYWORDS = {
    "RTL": ["RTL", "RETAIL", "RETAIL CHANNEL"],
    "WHL": ["WHL", "WHOLESALE", "WHOLESALE BROKER", "BROKER CHANNEL"],
    "DTC": ["DTC", "DIRECT TO CUSTOMER"],
    "CL1": ["CL1", "CORRESPONDENT", "CORRESPONDENT CHANNEL"]
}


def detect_channels_from_ac(ac_text: str):
    ac_upper = ac_text.upper()
    matched = []

    for channel, words in CHANNEL_KEYWORDS.items():
        for w in words:
            if w in ac_upper:
                matched.append(channel)
                break

    return matched


class TestCaseRAGRetriever:

    def __init__(self):
        self.openai_client = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT")
        )

        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
        )

        self.embedding_model = get("EMBEDDING_MODEL")
        self.top_k = get("TOP_K", int)

    def _build_query(self, user_story, description, ac):
        return f"""
User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}

Find relevant test cases.
"""

    def _embed_query(self, text):
        emb = self.openai_client.embeddings.create(
            model=self.embedding_model,
            input=text
        )
        return emb.data[0].embedding

    def retrieve(self, user_story, description, ac):
        query_vector = self._embed_query(
            self._build_query(user_story, description, ac)
        )

        channels = detect_channels_from_ac(ac)

        if channels:
            filters = [f"channel eq '{c}'" for c in channels]
            filter_expr = " or ".join(filters)
            print(f"🔎 Channels from AC: {channels}")
        else:
            filter_expr = None
            print("🔎 No channel → searching all")

        vector_query = {
            "vector": query_vector,
            "k": self.top_k,
            "fields": "embedding"
        }

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
                "content": r["content"]
            })

        return matches

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
