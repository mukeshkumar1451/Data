import logging
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
from config import get

logger = logging.getLogger(__name__)

def ensure_index():
    index_name = get("AZURE_SEARCH_INDEX")

    client = SearchIndexClient(
        endpoint=get("AZURE_SEARCH_ENDPOINT"),
        credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
    )

    existing = [idx.name for idx in client.list_indexes()]
    if index_name in existing:
        logger.info(f"✅ Index '{index_name}' already exists")
        return

    fields = [
        SimpleField(name="id", type=SearchFieldDataType.String, key=True),
        SimpleField(name="testCaseId", type=SearchFieldDataType.String, filterable=True),
        SearchableField(name="content", type=SearchFieldDataType.String),
        SearchField(
            name="embedding",
            type=SearchFieldDataType.Collection(SearchFieldDataType.Single),
            vector_search_dimensions=int(get("EMBEDDING_DIMENSIONS")),
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
            HnswAlgorithmConfiguration(name="hnsw-config")
        ]
    )

    index = SearchIndex(
        name=index_name,
        fields=fields,
        vector_search=vector_search
    )

    client.create_index(index)
    logger.info(f"✅ Index '{index_name}' created")
