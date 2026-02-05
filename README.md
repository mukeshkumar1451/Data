from azure.search.documents.indexes import SearchIndexClient
from azure.search.documents.indexes.models import (
    SearchIndex,
    SimpleField,
    SearchableField,
    SearchField,
    SearchFieldDataType,
    VectorSearch,
    VectorSearchProfile,
    HnswAlgorithmConfiguration
)
from azure.core.credentials import AzureKeyCredential

from embeddingtovectordb.config import get


def ensure_index():

    index_name = get("AZURE_SEARCH_INDEX")

    client = SearchIndexClient(
        endpoint=get("AZURE_SEARCH_ENDPOINT"),
        credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
    )

    existing = [idx.name for idx in client.list_indexes()]

    if index_name in existing:
        print(f"✅ Index '{index_name}' already exists")
        return

    print(f"🛠️ Creating index '{index_name}' ...")

    fields = [

        SimpleField(
            name="id",
            type=SearchFieldDataType.String,
            key=True
        ),

        SimpleField(
            name="testCaseId",
            type=SearchFieldDataType.String,
            filterable=True
        ),

        SimpleField(
            name="chunkId",
            type=SearchFieldDataType.Int32,
            filterable=True,
            sortable=True
        ),

        SimpleField(
            name="channel",
            type=SearchFieldDataType.String,
            filterable=True
        ),

        SearchableField(
            name="content",
            type=SearchFieldDataType.String
        ),

        SearchField(
            name="embedding",
            type=SearchFieldDataType.Collection(SearchFieldDataType.Single),
            vector_search_dimensions=3072,
            vector_search_profile_name="vector-profile"
        ),
    ]

    vector_search = VectorSearch(
        profiles=[
            VectorSearchProfile(
                name="vector-profile",
                algorithm_configuration_name="hnsw-config"
            )
        ],
        algorithms=[
            HnswAlgorithmConfiguration(
                name="hnsw-config"
            )
        ]
    )

    index = SearchIndex(
        name=index_name,
        fields=fields,
        vector_search=vector_search
    )

    client.create_index(index)

    print(f"✅ Index '{index_name}' created successfully")
