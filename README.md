from azure.search.documents.indexes import SearchIndexClient
from azure.search.documents.indexes.models import (
    SearchIndex,
    SimpleField,
    SearchableField,
    SearchField,
    SearchFieldDataType,
    VectorSearch,
    HnswAlgorithmConfiguration,
    VectorSearchProfile,
)
from azure.core.credentials import AzureKeyCredential
from config import get


def ensure_index():

    endpoint = f"https://{get('AZURE_SEARCH_SERVICE_NAME')}.search.windows.net"
    credential = AzureKeyCredential(get("AZURE_SEARCH_KEY"))
    index_name = get("AZURE_SEARCH_INDEX")

    client = SearchIndexClient(endpoint, credential)

    existing = [i.name for i in client.list_indexes()]
    if index_name in existing:
        print(f"Index '{index_name}' already exists ✔")
        return

    print(f"Creating index '{index_name}'...")

    fields = [
        SimpleField(name="id", type=SearchFieldDataType.String, key=True),

        SearchableField(name="content", type=SearchFieldDataType.String),

        SearchField(
            name="embedding",
            type=SearchFieldDataType.Collection(SearchFieldDataType.Single),
            searchable=True,
            vector_search_dimensions=3072,
            vector_search_profile_name="vector-profile",
        ),

        # LlamaIndex internal fields (IMPORTANT)
        SearchableField(name="metadata", type=SearchFieldDataType.String),
        SimpleField(name="doc_id", type=SearchFieldDataType.String, filterable=True),
        SearchableField(name="placeholder_metadata", type=SearchFieldDataType.String),
    ]

    vector_search = VectorSearch(
        algorithms=[HnswAlgorithmConfiguration(name="hnsw-config")],
        profiles=[VectorSearchProfile(name="vector-profile", algorithm_configuration_name="hnsw-config")],
    )

    index = SearchIndex(name=index_name, fields=fields, vector_search=vector_search)

    client.create_index(index)

    print("Index created successfully 🚀")
