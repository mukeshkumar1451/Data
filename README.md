import uuid
import logging
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

def upload(tc, channel_groups):
    logger.info(f"➡️ {tc}: merging all channels into ONE document")

    content = f"TestCase: {tc}\n\n"

    for channel, group in channel_groups:
        content += f"\n=========== CHANNEL: {channel} ===========\n"

        for _, row in group.iterrows():
            step = str(row.get("Test Step No.", "")).strip()
            if not step.startswith("Step"):
                continue

            content += f"""
Step: {step}

Description:
{row.get('Test Step Description','')}

Screen:
{row.get('Screen Name','')}

Test Data:
{row.get('Test Data','')}

Expected Result:
{row.get('Expected Results','')}

----------------------------------------
"""

    emb = openai_client.embeddings.create(
        model=get("EMBEDDING_MODEL"),
        input=content
    ).data[0].embedding

    doc = {
        "id": str(uuid.uuid4()),
        "testCaseId": tc,
        "content": content,
        "embedding": emb
    }

    search_client.upload_documents([doc])
    logger.info(f"✅ {tc} uploaded\n")
