llama-index
llama-index-vector-stores-azureaisearch
llama-index-embeddings-azure-openai
pandas
openpyxl
python-dotenv
azure-search-documents
---------------------------------------
# Azure OpenAI
AZURE_OPENAI_KEY=xxxx
AZURE_OPENAI_ENDPOINT=https://xxxx.openai.azure.com/
AZURE_OPENAI_API_VERSION=2024-02-15-preview
EMBEDDING_MODEL=text-embedding-3-large
EMBEDDING_DEPLOYMENT=embedding

# Azure AI Search
AZURE_SEARCH_SERVICE_NAME=your-search-name
AZURE_SEARCH_KEY=xxxxx
AZURE_SEARCH_INDEX=qa-teststeps-index
----------------------------------------------
import os
from dotenv import load_dotenv

load_dotenv()

def get(key: str):
    value = os.getenv(key)
    if not value:
        raise ValueError(f"Missing environment variable: {key}")
    return value
-------------------------------------
from llama_index.embeddings.azure_openai import AzureOpenAIEmbedding
from llama_index.core import Settings
from config import get

Settings.embed_model = AzureOpenAIEmbedding(
    model=get("EMBEDDING_MODEL"),
    deployment_name=get("EMBEDDING_DEPLOYMENT"),
    api_key=get("AZURE_OPENAI_KEY"),
    azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
    api_version=get("AZURE_OPENAI_API_VERSION"),
)
------------------------------------------
import pandas as pd
from llama_index.core import Document

COL_TC = "Test Case ID / Test Script ID"

def excel_to_documents(file_path):

    xls = pd.ExcelFile(file_path)
    documents = []

    for sheet in xls.sheet_names:
        channel = sheet.strip()

        df = pd.read_excel(xls, sheet_name=sheet)
        df.columns = df.columns.str.strip()

        # forward fill TC ID
        df[COL_TC] = df[COL_TC].ffill()

        for _, row in df.iterrows():

            step = str(row.get("Test Step No.", "")).strip()
            if not step.startswith("Step"):
                continue

            action = str(row.get("Test Step Description", ""))
            expected = str(row.get("Expected Results", ""))
            screen = str(row.get("Screen Name", ""))
            data = str(row.get("Test Data", ""))

            # Semantic embedding text
            text = f"""
User performs: {action}
On screen: {screen}
Using data: {data}
System should: {expected}
"""

            metadata = {
                "channel": channel,
                "testCaseId": str(row[COL_TC]),
                "stepNo": step,
                "screen": screen,
            }

            documents.append(Document(text=text, metadata=metadata))

    return documents
--------------------------------------------------------
from llama_index.vector_stores.azureaisearch import AzureAISearchVectorStore
from llama_index.core import StorageContext, VectorStoreIndex
from config import get
import settings  # loads embedding model


def build_index(documents):

    vector_store = AzureAISearchVectorStore(
        service_name=get("AZURE_SEARCH_SERVICE_NAME"),
        index_name=get("AZURE_SEARCH_INDEX"),
        api_key=get("AZURE_SEARCH_KEY"),
    )

    storage_context = StorageContext.from_defaults(vector_store=vector_store)

    index = VectorStoreIndex.from_documents(
        documents,
        storage_context=storage_context,
        show_progress=True
    )

    return index
---------------------------------------------------
import glob
from ingestion.excel_to_documents import excel_to_documents
from ingestion.build_index import build_index

all_docs = []

print("Reading Excel files...")

for file in glob.glob("data/excels/*.xlsx"):
    print("Processing:", file)
    docs = excel_to_documents(file)
    all_docs.extend(docs)

print(f"Total steps extracted: {len(all_docs)}")

print("Uploading to Azure AI Search via LlamaIndex...")
build_index(all_docs)

print("DONE — Vector DB ready 🚀")
-------------------------------------------
