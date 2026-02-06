from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from azure.search.documents.models import VectorizedQuery
from openai import AzureOpenAI

from embeddingtovectordb.config import get
from prompts.prompt_templates import build_testcase_prompt
from rerankerbase.llm_reranker import LLMReranker


class TestCaseRAGRetriever:

    def __init__(self):

        # -------- Azure OpenAI FIRST --------
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION")
        )

        self.chat_model = get("CHAT_MODEL")
        self.embed_model = get("EMBEDDING_MODEL")
        self.top_k = get("TOP_K", int)

        # -------- LLM Reranker (needs openai) --------
        self.reranker = LLMReranker(self.openai, self.chat_model)

        # -------- Azure AI Search --------
        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
        )
