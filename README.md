Index created successfully 🚀
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\Embedding\main_ingest.py", line 30, in <module> 
    build_index(docs)
    ~~~~~~~~~~~^^^^^^
  File "C:\Users\h84609n\Desktop\Embedding\ingestion\build_index.py", line 23, in build_index
    vector_store = AzureAISearchVectorStore(
        search_or_index_client=search_client,
    ...<3 lines>...
        doc_id_field_key="doc_id",
    )
TypeError: AzureAISearchVectorStore.__init__() missing 1 required positional argument: 'metadata_string_field_key'
(.venv) PS C:\Users\h84609n\Desktop\Embedding> 
