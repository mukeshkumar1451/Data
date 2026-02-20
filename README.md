Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\Embedding\main_ingest.py", line 30, in <module>
    build_index(docs)
    ~~~~~~~~~~~^^^^^^
  File "C:\Users\h84609n\Desktop\Embedding\ingestion\build_index.py", line 34, in build_index
    VectorStoreIndex.from_documents(
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^
        documents,
        ^^^^^^^^^^
        storage_context=storage_context,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        show_progress=True,
        ^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\core\indices\base.py", line 122, in from_documents
    return cls(
        nodes=nodes,
    ...<4 lines>...
        **kwargs,
    )
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\core\indices\vector_store\base.py", line 75, in __init__
    super().__init__(
    ~~~~~~~~~~~~~~~~^
        nodes=nodes,
        ^^^^^^^^^^^^
    ...<6 lines>...
        **kwargs,
        ^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\core\indices\base.py", line 79, in __init__
    index_struct = self.build_index_from_nodes(
        nodes + objects,  # type: ignore
        **kwargs,  # type: ignore
    )
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\core\indices\vector_store\base.py", line 309, in build_index_from_nodes
    return self._build_index_from_nodes(content_nodes, **insert_kwargs)
           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\core\indices\vector_store\base.py", line 278, in _build_index_from_nodes
    self._add_nodes_to_index(
    ~~~~~~~~~~~~~~~~~~~~~~~~^
        index_struct,
        ^^^^^^^^^^^^^
    ...<2 lines>...
        **insert_kwargs,
        ^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\core\indices\vector_store\base.py", line 232, in _add_nodes_to_index
    new_ids = self._vector_store.add(nodes_batch, **insert_kwargs)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\vector_stores\azureaisearch\base.py", line 958, in add
    self._search_client.index_documents(accumulator)
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\tracing\decorator.py", line 119, in wrapper_use_tracer
    return func(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\search\documents\_search_client.py", line 685, in index_documents
    return self._index_documents_actions(actions=batch.actions, **kwargs)        
           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^        
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\search\documents\_search_client.py", line 695, in _index_documents_actions
    batch_response = self._client.documents.index(
        batch=batch, error_map=error_map, **kwargs
    )
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\tracing\decorator.py", line 119, in wrapper_use_tracer
    return func(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\search\documents\_generated\operations\_documents_operations.py", line 1455, in index    
    raise HttpResponseError(response=response, model=error)
azure.core.exceptions.HttpResponseError: () The request is invalid. Details: The 
property 'placeholder_metadata' does not exist on type 'search.documentFields' or is not present in the API version '2025-09-01'. Make sure to only use property names that are defined by the type.
Code:
Message: The request is invalid. Details: The property 'placeholder_metadata' does not exist on type 'search.documentFields' or is not present in the API version 
'2025-09-01'. Make sure to only use property names that are defined by the type. 
(.venv) PS C:\Users\h84609n\Desktop\Embedding> 
