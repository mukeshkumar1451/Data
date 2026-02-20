# main.py
import glob
import logging
import os

from config import get
from index_manager import ensure_index

from excel_reader import read_excel
from vector_uploader import (
    upload,
    flush_batch
)

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s"
)

logger = logging.getLogger(__name__)


# ---------------------------------------------------
# File Type Detection
# ---------------------------------------------------
def is_test_script(file_name: str):
    name = file_name.upper()
    return "TEST SCRIPT" in name or "TEST SCRIPTS" in name



# ---------------------------------------------------
# Main Runner
# ---------------------------------------------------
def main():
    logger.info("🚀 Starting Vector Upload")

    ensure_index()

    input_dir = get("INPUT_DIR")

    for file in glob.glob(f"{input_dir}/*"):
        file_name = os.path.basename(file)

        logger.info(f"📄 Processing file: {file_name}")

        try:
            # ---------------- EXCEL ----------------
            if file.lower().endswith(".xlsx"):

                if is_test_script(file_name):
                    logger.info("📘 Detected TEST SCRIPT Excel")
                    for tc, channel_groups in read_excel(file):
                        upload(tc, channel_groups)
                        
                

            
        except Exception as e:
            logger.exception(f"❌ Error processing {file_name}: {str(e)}")

    flush_batch()

    logger.info("🎉 Upload completed successfully")


if __name__ == "__main__":
    main()
---------------------------------------------------------------------
# index_manager.py
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
            searchable=True,
            vector_search_dimensions=int(get("EMBEDDING_DIM")),
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
-----------------------------------------------------------------------------------------------
# excel_reader.py
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
-------------------------------------------------------------------------
# vector_uploader.py
import uuid
import logging
from collections import OrderedDict
from openai import AzureOpenAI
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from config import get


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

BATCH_SIZE = 50
pending_docs = []
pending_texts = []


def upload(tc, channel_groups):
    global pending_docs, pending_texts

    channels = [c for c, _ in channel_groups]

    step_map = OrderedDict()

    for _, group in channel_groups:
        for _, row in group.iterrows():
            step_no = str(row.get("Test Step No.", "")).strip()
            if not step_no.startswith("Step"):
                continue

            if step_no not in step_map:
                step_map[step_no] = {
                    "description": row.get("Test Step Description", ""),
                    "screen": row.get("Screen Name", ""),
                    "data": row.get("Test Data", ""),
                    "expected": row.get("Expected Results", "")
                }

    content = f"\nTestCase: {tc}\n\n=========== TEST STEPS ===========\n"

    for step_no, d in step_map.items():
        content += f"""
{step_no}
Description: {d['description']}
Screen: {d['screen']}
Data: {d['data']}
Expected: {d['expected']}
"""

    pending_texts.append(content)

    pending_docs.append({
        "id": str(uuid.uuid4()),
        "testCaseId": tc,
        "channels": channels,
        "content": content,
        "embedding": None
    })

    if len(pending_docs) >= BATCH_SIZE:
        flush_batch()




def flush_batch():
    global pending_docs, pending_texts

    if not pending_docs:
        return

    logger.info(f"🚀 Uploading batch of {len(pending_docs)}")

    embeddings = openai_client.embeddings.create(
        model=get("EMBEDDING_MODEL"),
        input=pending_texts
    ).data

    for doc, emb in zip(pending_docs, embeddings):
        doc["embedding"] = emb.embedding

    results = search_client.upload_documents(pending_docs)

    for r in results:
        if not r.succeeded:
            logger.error(f"Failed: {r.key} | {r.error_message}")

    pending_docs.clear()
    pending_texts.clear()


		
	







