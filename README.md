Total steps extracted: 0
Uploading to Azure AI Search via LlamaIndex...
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\Embedding\main_ingest.py", line 17, in <module>
    build_index(all_docs)
    ~~~~~~~~~~~^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\Embedding\ingestion\build_index.py", line 9, in 
build_index
    vector_store = AzureAISearchVectorStore(
        service_name=get("AZURE_SEARCH_SERVICE_NAME"),
        index_name=get("AZURE_SEARCH_INDEX"),
        api_key=get("AZURE_SEARCH_KEY"),
    )
TypeError: AzureAISearchVectorStore.__init__() missing 6 required positional arguments: 'search_or_index_client', 'id_field_key', 'chunk_field_key', 'embedding_field_key', 'metadata_string_field_key', and 'doc_id_field_key'
