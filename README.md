import pandas as pd
from collections import defaultdict

def read_excel(file_path):
    xls = pd.ExcelFile(file_path)

    COL_TC = "Test Case ID / Test Script ID"
    tc_map = defaultdict(list)

    for sheet in xls.sheet_names:
        channel = sheet.strip()

        df = pd.read_excel(xls, sheet_name=sheet)
        df.columns = df.columns.str.strip()
        df[COL_TC] = df[COL_TC].ffill()

        grouped = df.groupby(COL_TC)

        for tc, group in grouped:
            tc_map[tc].append((channel, group))

    for tc, channel_groups in tc_map.items():
        yield tc, channel_groups
-----------------------------------------------------------
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

def ensure_index():
    client = SearchIndexClient(
        endpoint=get("AZURE_SEARCH_ENDPOINT"),
        credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
    )

    index_name = get("AZURE_SEARCH_INDEX")
    existing = [i.name for i in client.list_indexes()]
    if index_name in existing:
        return

    fields = [
        SimpleField(name="id", type=SearchFieldDataType.String, key=True),
        SimpleField(name="testCaseId", type=SearchFieldDataType.String, filterable=True),

        SimpleField(
            name="channels",
            type=SearchFieldDataType.Collection(SearchFieldDataType.String),
            filterable=True
        ),

        SearchableField(name="content", type=SearchFieldDataType.String),

        SearchField(
            name="embedding",
            type=SearchFieldDataType.Collection(SearchFieldDataType.Single),
            vector_search_dimensions=int(get("EMBEDDING_DIMENSIONS")),
            vector_search_profile_name="vector-profile"
        ),
    ]

    vector_search = VectorSearch(
        profiles=[VectorSearchProfile(
            name="vector-profile",
            algorithm_configuration_name="hnsw-config"
        )],
        algorithms=[HnswAlgorithmConfiguration(name="hnsw-config")]
    )

    index = SearchIndex(
        name=index_name,
        fields=fields,
        vector_search=vector_search
    )

    client.create_index(index)
