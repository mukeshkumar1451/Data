import os
import uuid
import pandas as pd
from dotenv import load_dotenv
from openai import AzureOpenAI
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential

load_dotenv()

# ========= Read from ENV =========
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

# ========= Read Excel =========
df = pd.read_excel(EXCEL_PATH)

COL_USERSTORY = "UserStoryID"
COL_TESTCASE = "TestCaseID"
COL_MODULE = "Module"
COL_CHANNEL = "Channel"
COL_STEPS = "TestSteps"
COL_EXPECTED = "ExpectedResult"

for _, row in df.iterrows():
    content = f"""
    Test Case ID: {row[COL_TESTCASE]}
    User Story: {row[COL_USERSTORY]}
    Steps: {row[COL_STEPS]}
    Expected Result: {row[COL_EXPECTED]}
    """

    emb = openai_client.embeddings.create(
        model=EMBED_DEPLOYMENT,
        input=content
    ).data[0].embedding

    doc = {
        "id": str(uuid.uuid4()),
        "userStoryId": str(row[COL_USERSTORY]),
        "testCaseId": str(row[COL_TESTCASE]),
        "module": str(row[COL_MODULE]),
        "channel": str(row[COL_CHANNEL]),
        "content": content,
        "embedding": emb
    }

    search_client.upload_documents([doc])

print("✅ Uploaded Excel to Vector DB using ENV config")
