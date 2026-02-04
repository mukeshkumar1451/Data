import os
import uuid
import pandas as pd
from dotenv import load_dotenv
from openai import AzureOpenAI
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential

# ========= Load ENV =========
load_dotenv()

OPENAI_ENDPOINT = os.getenv("AZURE_OPENAI_ENDPOINT")
OPENAI_KEY = os.getenv("AZURE_OPENAI_KEY")
EMBED_DEPLOYMENT = os.getenv("AZURE_OPENAI_EMBED_DEPLOYMENT")

SEARCH_ENDPOINT = os.getenv("AZURE_SEARCH_ENDPOINT")
SEARCH_KEY = os.getenv("AZURE_SEARCH_KEY")
INDEX_NAME = os.getenv("AZURE_SEARCH_INDEX")

EXCEL_PATH = "Indiv_US_718521_Test Scripts_v1.0.xlsx"

# ========= Clients =========
openai_client = AzureOpenAI(
    api_key=OPENAI_KEY,
    api_version="2024-02-01",
    azure_endpoint=OPENAI_ENDPOINT
)

search_client = SearchClient(
    endpoint=SEARCH_ENDPOINT,
    index_name=INDEX_NAME,
    credential=AzureKeyCredential(SEARCH_KEY)
)

# ========= Process Excel =========
xls = pd.ExcelFile(EXCEL_PATH)

for sheet in xls.sheet_names:
    print(f"\n📄 Processing sheet: {sheet}")

    df = pd.read_excel(xls, sheet_name=sheet)
    df.columns = df.columns.str.strip()  # remove hidden spaces

    # Column mappings (after strip)
    COL_TESTCASE = "Test Case ID / Test Script ID"
    COL_SCENARIO = "Test Scenario Id"
    COL_SCENARIO_DESC = "Test Scenario Description"
    COL_SCRIPT_DESC = "Test Script Description"
    COL_PRECONDITION = "Pre-Condition & Assumptions"
    COL_STEP_NO = "Test Step No."
    COL_STEP_DESC = "Test Step Description"
    COL_SCREEN = "Screen Name"
    COL_TESTDATA = "Test Data"
    COL_EXPECTED = "Expected Results"
    COL_STATUS = "Status"
    COL_REQ_MAP = "Requirement Mapping"

    channel = sheet.strip()  # RTL / WHL / DTC / CL1

    for _, row in df.iterrows():
        try:
            content = f"""
            Test Case: {row.get(COL_TESTCASE, '')}
            Scenario: {row.get(COL_SCENARIO_DESC, '')}
            Script: {row.get(COL_SCRIPT_DESC, '')}
            Precondition: {row.get(COL_PRECONDITION, '')}

            Step {row.get(COL_STEP_NO, '')}:
            {row.get(COL_STEP_DESC, '')}

            Screen: {row.get(COL_SCREEN, '')}
            Test Data: {row.get(COL_TESTDATA, '')}
            Expected: {row.get(COL_EXPECTED, '')}
            """

            # ===== Create embedding =====
            emb = openai_client.embeddings.create(
                model=EMBED_DEPLOYMENT,
                input=content
            ).data[0].embedding

            document = {
                "id": str(uuid.uuid4()),
                "testCaseId": str(row.get(COL_TESTCASE, "")),
                "scenarioId": str(row.get(COL_SCENARIO, "")),
                "screenName": str(row.get(COL_SCREEN, "")),
                "status": str(row.get(COL_STATUS, "")),
                "requirementMapping": str(row.get(COL_REQ_MAP, "")),
                "channel": channel,
                "content": content,
                "embedding": emb
            }

            search_client.upload_documents([document])
            print(f"✅ Uploaded Step {row.get(COL_STEP_NO, '')} from {channel}")

        except Exception as e:
            print("❌ Error:", e)

print("\n🎉 All sheets uploaded to Azure AI Search vector DB")
