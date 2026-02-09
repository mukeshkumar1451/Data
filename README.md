import uuid
from collections import OrderedDict
from openai import AzureOpenAI
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from embeddingtovectordb.config import get
from precondition_extractor import PreconditionExtractor


# ---------------- Azure Clients ----------------

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


# ---------------- Precondition Builder ----------------

def build_preconditions(channel_groups, ac_text):
    """
    Build channel-specific preconditions using AC text.
    Order is always RTL → WHL → DTC → CL1
    """

    ORDER = ["RTL", "WHL", "DTC", "CL1"]
    group_map = {c: g for c, g in channel_groups}

    pre_data = PreconditionExtractor.extract(ac_text)

    loan_purpose = pre_data["loan_purpose"]
    loan_type = pre_data["loan_type"]
    product_code = pre_data["product_code"]
    loan_stage = pre_data["loan_stage"]

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

    # Add loan knowledge for LLM reasoning
    pre += """
=========== LOAN TYPE & PRODUCT KNOWLEDGE ===========

Loan Types:
Conventional, FHA, VA, USDA, Heloc

Products Mapping:
Conventional → CF30, CF10, CF15
Conventional Jumbo → JCPF30, JEG10A, JF30B
VA → VF30, VF15
FHA → FF30
Heloc → NRZHeloc
Non QM → NRSEF30, NRSVF30

======================================================
"""

    return pre


# ---------------- Upload Function ----------------

def upload(tc, channel_groups, ac_text):
    """
    Create ONE clean, high-quality document per TestCase
    """

    channels = [c for c, _ in channel_groups]

    # --------------------------------------------
    # Deduplicate step text (same across channels)
    # --------------------------------------------
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

    # --------------------------------------------
    # Build Content for Embedding
    # --------------------------------------------
    content = f"\nTestCase: {tc}\n"
    content += build_preconditions(channel_groups, ac_text)
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

    # --------------------------------------------
    # Create Embedding
    # --------------------------------------------
    emb = openai_client.embeddings.create(
        model=get("EMBEDDING_MODEL"),
        input=content
    ).data[0].embedding

    # --------------------------------------------
    # Upload Document
    # --------------------------------------------
    doc = {
        "id": str(uuid.uuid4()),
        "testCaseId": tc,
        "channels": channels,
        "content": content,
        "embedding": emb
    }

    search_client.upload_documents([doc])
