# vector_uploader.py

import uuid
import logging
from collections import OrderedDict
from openai import AzureOpenAI
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from config import get

logger = logging.getLogger(__name__)

# ---------------------------------------------------
# Azure OpenAI Client
# ---------------------------------------------------
openai_client = AzureOpenAI(
    api_key=get("AZURE_OPENAI_KEY"),
    api_version=get("AZURE_OPENAI_API_VERSION"),
    azure_endpoint=get("AZURE_OPENAI_ENDPOINT")
)

# ---------------------------------------------------
# Azure AI Search Client
# ---------------------------------------------------
search_client = SearchClient(
    endpoint=get("AZURE_SEARCH_ENDPOINT"),
    index_name=get("AZURE_SEARCH_INDEX"),
    credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
)

# ---------------------------------------------------
# Batch Config
# ---------------------------------------------------
BATCH_SIZE = 50
pending_docs = []
pending_texts = []


# ---------------------------------------------------
# Upload Function (TestCase-Level Storage)
# ---------------------------------------------------
def upload(tc, channel_groups):
    """
    Creates ONE vector document per Test Case.
    Includes Pre-Condition & Assumptions.
    """

    global pending_docs, pending_texts

    channels = [c for c, _ in channel_groups]

    step_map = OrderedDict()
    precondition = ""

    for _, group in channel_groups:

        # Normalize column names (important for Excel issues)
        group.columns = group.columns.str.strip()

        # ---------------------------------------------------
        # Extract Pre-Condition & Assumptions
        # ---------------------------------------------------
        precondition_col = "Pre-Condition & Assumptions"

        if precondition_col in group.columns:
            non_empty = group[precondition_col].dropna()
            if not non_empty.empty:
                precondition = str(non_empty.iloc[0])

        # ---------------------------------------------------
        # Process Steps
        # ---------------------------------------------------
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

    # ---------------------------------------------------
    # Build Final Content
    # ---------------------------------------------------
    content = f"\nTestCase: {tc}\n"
    content += f"Channels: {', '.join(channels)}\n\n"

    if precondition:
        content += f"Pre-Condition & Assumptions:\n{precondition}\n\n"

    content += "=========== TEST STEPS ===========\n"

    for step_no, d in step_map.items():
        content += f"""
{step_no}
Description: {d['description']}
Screen: {d['screen']}
Test Data: {d['data']}
Expected Result: {d['expected']}
"""

    # Add to batch
    pending_texts.append(content)

    pending_docs.append({
        "id": str(uuid.uuid4()),
        "testCaseId": tc,
        "channels": channels,
        "content": content,
        "embedding": None
    })

    # Flush if batch size reached
    if len(pending_docs) >= BATCH_SIZE:
        flush_batch()


# ---------------------------------------------------
# Flush Batch to Azure AI Search
# ---------------------------------------------------
def flush_batch():
    global pending_docs, pending_texts

    if not pending_docs:
        return

    logger.info(f"🚀 Uploading batch of {len(pending_docs)} documents")

    # Generate embeddings
    response = openai_client.embeddings.create(
        model=get("EMBEDDING_MODEL"),
        input=pending_texts
    )

    embeddings = response.data

    for doc, emb in zip(pending_docs, embeddings):
        doc["embedding"] = emb.embedding

    # Upload to Azure AI Search
    results = search_client.upload_documents(pending_docs)

    for r in results:
        if not r.succeeded:
            logger.error(f"❌ Failed: {r.key} | {r.error_message}")

    pending_docs.clear()
    pending_texts.clear()

    logger.info("✅ Batch upload complete")
