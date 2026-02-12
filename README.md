from azure.search.documents.indexes import SearchIndexClient
from azure.search.documents.indexes.models import (
    SearchIndex,
    SearchField,
    SearchFieldDataType,
    SimpleField,
    SearchableField,
    VectorSearch,
    HnswAlgorithmConfiguration,
    VectorSearchProfile
)
from azure.core.credentials import AzureKeyCredential
from config import AZURE_SEARCH_ENDPOINT, AZURE_SEARCH_KEY, AZURE_SEARCH_INDEX


def create_vector_index():

    credential = AzureKeyCredential(AZURE_SEARCH_KEY)
    client = SearchIndexClient(
        endpoint=AZURE_SEARCH_ENDPOINT,
        credential=credential
    )

    fields = [

        SimpleField(name="id", type=SearchFieldDataType.String, key=True),

        SearchableField(name="content", type=SearchFieldDataType.String),

        SearchField(
            name="embedding",
            type=SearchFieldDataType.Collection(SearchFieldDataType.Single),
            searchable=True,
            vector_search_dimensions=3072,
            vector_search_profile_name="vector-profile"
        ),

        SimpleField(name="channel", type=SearchFieldDataType.String, filterable=True, facetable=True),
        SimpleField(name="knowledgeType", type=SearchFieldDataType.String, filterable=True, facetable=True),
        SimpleField(name="stage", type=SearchFieldDataType.String, filterable=True, facetable=True),
        SimpleField(name="subStage", type=SearchFieldDataType.String, filterable=True),
        SimpleField(name="role", type=SearchFieldDataType.String, filterable=True),
        SimpleField(name="system", type=SearchFieldDataType.String, filterable=True),

        SimpleField(name="nextStage", type=SearchFieldDataType.String, filterable=True),
        SimpleField(name="orderIndex", type=SearchFieldDataType.Int32, filterable=True, sortable=True),
        SimpleField(name="pageNumber", type=SearchFieldDataType.Int32, filterable=True),
        SimpleField(name="sheetName", type=SearchFieldDataType.String, filterable=True),

        SimpleField(name="stepNumber", type=SearchFieldDataType.String, filterable=True),
        SimpleField(name="feature", type=SearchFieldDataType.String, filterable=True),
        SearchableField(name="expectedResult", type=SearchFieldDataType.String),

        SimpleField(name="testCaseId", type=SearchFieldDataType.String, filterable=True),
        SimpleField(name="scenario", type=SearchFieldDataType.String, filterable=True),
        SimpleField(name="priority", type=SearchFieldDataType.String, filterable=True),
        SimpleField(name="activeFlag", type=SearchFieldDataType.Boolean, filterable=True)
    ]

    vector_search = VectorSearch(
        algorithms=[
            HnswAlgorithmConfiguration(name="hnsw")
        ],
        profiles=[
            VectorSearchProfile(
                name="vector-profile",
                algorithm_configuration_name="hnsw"
            )
        ]
    )

    index = SearchIndex(
        name=AZURE_SEARCH_INDEX,
        fields=fields,
        vector_search=vector_search
    )

    client.create_or_update_index(index)
    print(f"✅ Index '{AZURE_SEARCH_INDEX}' created successfully.")


if __name__ == "__main__":
    create_vector_index()

