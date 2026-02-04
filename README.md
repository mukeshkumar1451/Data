Now to Set up RAG Pipeline
Create an Environment Configuration File (So that the RAG pipeline can be re-used across usecases in NewRes)
Store all pipeline parameters:
o	DB connection details
o	Embedding model name & dimension
o	Chunk size & overlap strategy 
o	Top‑K retrieval value (K=40)
o	Re-ranking threshold (50%)
o	Path locations for exports/imports (if any)
Environment file format: .env or YAML.
------------------------------------------
# config.yaml template for GHCP Reusable RAG Pipeline
VECTOR_DB: qdrant-library
QDRANT_PATH: ./qdrant_db  # Local file-based Qdrant DB
QDRANT_COLLECTION: testcases
EMBEDDING_MODEL: fastembed
EMBEDDING_MODEL_NAME: BAAI/bge-small-en-v1.5
EMBEDDING_DIM: 384
LLM: copilot-chat
CHUNK_STRATEGY: row-level  # One embedding per test case (row)
CHUNK_SIZE: 0  # Not used, row-level only
CHUNK_OVERLAP: 0  # Not used, row-level only
TOP_K: 40
RERANK_THRESHOLD: 0.5
CONTEXT_DIR: ./context
EXPORT_DIR: ./export
