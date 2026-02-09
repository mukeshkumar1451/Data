from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from embeddingtovectordb.config import get

search_client = SearchClient(
    endpoint=get("AZURE_SEARCH_ENDPOINT"),
    index_name=get("AZURE_SEARCH_INDEX"),
    credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
)

result = search_client.search(
    search_text="*",
    include_total_count=True,
    top=1
)

print("Total documents in index:", result.get_count())
