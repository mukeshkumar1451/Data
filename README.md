import uuid
from collections import OrderedDict
from openai import AzureOpenAI
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from embeddingtovectordb.config import get
from precondition_extractor import PreconditionExtractor


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


PRE_COL = "Pre-Condition & Assumptions"


def build_preconditions(channel_groups):
    ORDER = ["RTL", "WHL", "DTC", "CL1"]
    group_map = {c: g for c, g in channel_groups}

    # Get precondition text from any sheet (same TC)
    sample_group = list(group_map.values())[0]

    pre_text = (
        sample_group[PRE_COL]
        .dropna()
        .astype(str)
        .iloc[0]
    )

    loan_purpose, loan_type, product_code, loan_stage = \
        PreconditionExtractor.extract(pre_text)

    pre = "\n=========== PRE-CONDITIONS ===========\n"

    for channel in ORDER:
        if channel not in group_map:
            continue

        if channel == "RTL":
            portal = "Customer Portal"
        elif channel in ["WHL", "CL1"]:
            portal = "Broker Portal"
        elif channel == "DTC":
            portal = "Ignite Portal"
        else:
            portal = ""

        pre += f"""
{channel}:
Create a loan from {portal} as per pre-conditions below:
Channel: {channel}
Loan Purpose: {loan_purpose}
Loan Type: {loan_type}
Product Code: {product_code}
Loan Stage: {loan_stage}

----------------------------------------
"""

    return pre


def upload(tc, channel_groups):
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

    content = f"\nTestCase: {tc}\n"
    content += build_preconditions(channel_groups)
    content += "\n=========== TEST STEPS ===========\n"

    for step_no, d in step_map.items():
        content += f"""
{step_no}

Description:
{d['description']}

Screen:
{d['screen']}

Test Data:
{d['data']}

Expected Result:
{d['expected']}

----------------------------------------
"""

    emb = openai_client.embeddings.create(
        model=get("EMBEDDING_MODEL"),
        input=content
    ).data[0].embedding

    doc = {
        "id": str(uuid.uuid4()),
        "testCaseId": tc,
        "channels": channels,
        "content": content,
        "embedding": emb
    }

    search_client.upload_documents([doc])
