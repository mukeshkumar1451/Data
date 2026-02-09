#config.py
import os
from dotenv import load_dotenv
from openai import AzureOpenAI
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential

load_dotenv()


def get(key, cast=str):
    val=os.getenv(key)
    return cast(val) if val and cast != str else val    


---------------------------------------------------
# excel_reader.py
import pandas as pd
import logging

logger = logging.getLogger(__name__)

def read_excel(file_path):
    xls = pd.ExcelFile(file_path)

    for sheet in xls.sheet_names:
        channel = sheet.strip()   # ✅ THIS IS YOUR CHANNEL

        df = pd.read_excel(xls, sheet_name=sheet)
        df.columns = df.columns.str.strip()

        COL_TC = "Test Case ID / Test Script ID"
        COL_STEP = "Test Step No."

        # 🔥 Fix merged TestCaseId cells
        df[COL_TC] = df[COL_TC].ffill()

        grouped = df.groupby(COL_TC)

        for tc, group in grouped:
            step_count = (
                group[COL_STEP]
                .astype(str)
                .str.strip()
                .str.startswith("Step")
                .sum()
            )

            logger.info(f" sheet='{channel}' | test_case_id='{tc}' | steps={step_count}")

            # ✅ return channel
            yield channel, tc, group, step_count


            ------------------------------------------------
            # index_manager.py
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

from embeddingtovectordb.config import get

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

    logger.info(f"🛠️ Creating index '{index_name}' ...")

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

    logger.info(f"✅ Index '{index_name}' created successfully")
----------------------------------------------------------------------
# vector_uploader.py
import uuid
import logging
from openai import AzureOpenAI
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from embeddingtovectordb.config import get

logger = logging.getLogger(__name__)

openai_client = AzureOpenAI(
    api_key=get("AZURE_OPENAI_KEY"),
    api_version=get("AZURE_OPENAI_API_VERSION"),
    azure_endpoint=get("AZURE_OPENAI_ENDPOINT")
)

search_client = SearchClient(
    endpoint=get("AZURE_SEARCH_ENDPOINT"),
    index_name=get("AZURE_SEARCH_INDEX"),
    credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
)

MAX_STEPS = get("MAX_STEPS_PER_CHUNK", int)


def build_chunks(group):
    chunks, current, count = [], "", 0

    for _, row in group.iterrows():
        step = str(row.get("Test Step No.", "")).strip()
        if not step.startswith("Step"):
            continue

        block = f"""{step}
{row.get('Test Step Description','')}
Screen: {row.get('Screen Name','')}
Data: {row.get('Test Data','')}
Expected: {row.get('Expected Results','')}

"""
        current += block
        count += 1

        if count == MAX_STEPS:
            chunks.append(current)
            current, count = "", 0

    if current:
        chunks.append(current)

    return chunks


def upload(sheet, tc, group, step_count):
    chunks = build_chunks(group)

    logger.info(f"➡️ {tc}: {'Single' if step_count<=MAX_STEPS else 'Multi'} chunk ({len(chunks)})")

    for idx, text in enumerate(chunks, 1):
        content = f"TestCase: {tc}\nChannel: {sheet}\nChunk:{idx}\n\n{text}"

        emb = openai_client.embeddings.create(
            model=get("EMBEDDING_MODEL"),
            input=content
        ).data[0].embedding

        doc = {
            "id": str(uuid.uuid4()),
            "testCaseId": tc,
            "chunkId": idx,
            "channel": sheet,
            "content": content,
            "embedding": emb
        }

        search_client.upload_documents([doc])

    logger.info(f"✅ {tc} uploaded\n")
