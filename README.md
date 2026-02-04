# index_manager.py
from azure.core.credentials import AzureKeyCredential
from azure.search.documents.indexes import SearchIndexClient
from azure.search.documents.indexes.models import (
    SearchIndex,
    SimpleField,
    SearchField,
    SearchFieldDataType,
    VectorSearch,
    HnswAlgorithmConfiguration,
    VectorSearchProfile
)


def ensure_index_exists(endpoint, key, index_name):
    client = SearchIndexClient(endpoint, AzureKeyCredential(key))

    existing_indexes = [idx.name for idx in client.list_indexes()]

    if index_name in existing_indexes:
        print(f"✅ Index '{index_name}' already exists — proceeding to upload\n")
        return

    print(f"🛠️ Index '{index_name}' not found — creating now...\n")

    # -------- Vector Search Config --------
    vector_search = VectorSearch(
        algorithms=[
            HnswAlgorithmConfiguration(
                name="hnsw-config",
                parameters={
                    "m": 8,
                    "efConstruction": 400,
                    "efSearch": 100,
                    "metric": "cosine"
                }
            )
        ],
        profiles=[
            VectorSearchProfile(
                name="vector-profile",
                algorithm_configuration_name="hnsw-config"
            )
        ]
    )

    # -------- Index Fields --------
    fields = [
        SimpleField(name="id", type=SearchFieldDataType.String, key=True),

        SimpleField(
            name="testCaseId",
            type=SearchFieldDataType.String,
            filterable=True,
            retrievable=True
        ),

        SimpleField(
            name="chunkId",
            type=SearchFieldDataType.Int32,
            filterable=True,
            sortable=True,
            retrievable=True
        ),

        SimpleField(
            name="requirementMapping",
            type=SearchFieldDataType.String,
            filterable=True,
            retrievable=True
        ),

        SimpleField(
            name="channel",
            type=SearchFieldDataType.String,
            filterable=True,
            retrievable=True
        ),

        SearchField(
            name="content",
            type=SearchFieldDataType.String,
            searchable=True,
            retrievable=True,
            analyzer_name="standard.lucene"
        ),

        SearchField(
            name="embedding",
            type=SearchFieldDataType.Collection(SearchFieldDataType.Single),
            vector_search_dimensions=3072,
            vector_search_profile_name="vector-profile"
        ),
    ]

    # -------- Create Index --------
    index = SearchIndex(
        name=index_name,
        fields=fields,
        vector_search=vector_search
    )

    client.create_index(index)

    print(f"✅ Index '{index_name}' created successfully with chunkId support\n")
