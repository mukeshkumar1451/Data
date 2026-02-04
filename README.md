# ================== RAG PIPELINE CONFIG ==================

# -------- Azure AI Search (Vector DB) --------
VECTOR_DB: azure-ai-search
AZURE_SEARCH_ENDPOINT: ${AZURE_SEARCH_ENDPOINT}
AZURE_SEARCH_KEY: ${AZURE_SEARCH_KEY}
AZURE_SEARCH_INDEX: testcase-vectordb

# -------- Azure OpenAI --------
AZURE_OPENAI_ENDPOINT: ${AZURE_OPENAI_ENDPOINT}
AZURE_OPENAI_KEY: ${AZURE_OPENAI_KEY}
AZURE_OPENAI_API_VERSION: 2024-02-15-preview

EMBEDDING_MODEL_DEPLOYMENT: text-embedding-3-large
EMBEDDING_DIM: 3072

CHAT_MODEL_DEPLOYMENT: gpt-4o

# -------- Chunking Strategy --------
CHUNK_STRATEGY: testcase-step-aware
MAX_STEPS_PER_CHUNK: 8
CHUNK_OVERLAP: 0

# -------- Retrieval --------
TOP_K: 40
RERANK_THRESHOLD: 0.5

# -------- Metadata Fields in Index --------
INDEX_FIELDS:
  - testCaseId
  - chunkId
  - channel
  - requirementMapping

# -------- Paths --------
EXCEL_INPUT_DIR: ./excel
CONTEXT_DIR: ./context
EXPORT_DIR: ./export
