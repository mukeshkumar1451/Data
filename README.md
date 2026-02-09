import uuid
from collections import OrderedDict
from openai import AzureOpenAI
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from embeddingtovectordb.config import get

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
    """
    Build ONE clean content for the testcase by removing
    duplicate steps coming from multiple channel sheets.
    """

    # ---------------------------------------------------
    # Collect all steps across channels by step number
    # ---------------------------------------------------
    step_map = OrderedDict()
    channels = []

    for channel, group in channel_groups:
        channels.append(channel)

        for _, row in group.iterrows():
            step_no = str(row.get("Test Step No.", "")).strip()

            if not step_no.startswith("Step"):
                continue

            # Use step number as unique key
            if step_no not in step_map:
                step_map[step_no] = {
                    "description": row.get("Test Step Description", ""),
                    "screen": row.get("Screen Name", ""),
                    "data": row.get("Test Data", ""),
                    "expected": row.get("Expected Results", "")
                }

    # ---------------------------------------------------
    # Build CLEAN content (no channel repetition)
    # ---------------------------------------------------
    content = f"""
TestCase: {tc}
Applicable Channels: {", ".join(channels)}

================= TEST STEPS =================
"""

    for step_no, details in step_map.items():
        content += f"""
{step_no}

Description:
{details['description']}

Screen:
{details['screen']}

Test Data:
{details['data']}

Expected Result:
{details['expected']}

----------------------------------------
"""

    # ---------------------------------------------------
    # Create embedding from CLEAN content
    # ---------------------------------------------------
    emb = openai_client.embeddings.create(
        model=get("EMBEDDING_MODEL"),
        input=content
    ).data[0].embedding

    doc = {
        "id": str(uuid.uuid4()),
        "testCaseId": tc,
        "channels": channels,   # metadata only
        "content": content,
        "embedding": emb
    }

    search_client.upload_documents([doc])
