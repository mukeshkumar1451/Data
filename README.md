from azure.core.credentials import AzureKeyCredential
from azure.search.documents.indexes import SearchIndexClient
from azure.search.documents.indexes.models import *
from config import get

def ensure_index():
    endpoint = get("AZURE_SEARCH_ENDPOINT")
    key = get("AZURE_SEARCH_KEY")
    index_name = get("AZURE_SEARCH_INDEX")

    client = SearchIndexClient(endpoint, AzureKeyCredential(key))

    if index_name in [i.name for i in client.list_indexes()]:
        print(f"✅ Index exists: {index_name}")
        return

    vector_search = VectorSearch(
        algorithms=[HnswAlgorithmConfiguration(
            name="hnsw-config",
            parameters={
                "m": 8,
                "efConstruction": 400,
                "efSearch": 100,
                "metric": "cosine"
            }
        )],
        profiles=[VectorSearchProfile(
            name="vector-profile",
            algorithm_configuration_name="hnsw-config"
        )]
    )

    fields = [
        SimpleField("id", SearchFieldDataType.String, key=True),
        SimpleField("testCaseId", SearchFieldDataType.String, filterable=True),
        SimpleField("chunkId", SearchFieldDataType.Int32, filterable=True, sortable=True),
        SimpleField("channel", SearchFieldDataType.String, filterable=True),
        SearchField("content", SearchFieldDataType.String, searchable=True, analyzer_name="standard.lucene"),
        SearchField(
            "embedding",
            SearchFieldDataType.Collection(SearchFieldDataType.Single),
            vector_search_dimensions=get("EMBEDDING_DIM", int),
            vector_search_profile_name="vector-profile"
        ),
    ]

    index = SearchIndex(index_name, fields=fields, vector_search=vector_search)
    client.create_index(index)
    print("✅ Index created")
