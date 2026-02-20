Index 'mortgage-knowledge-index1' already exists ✔
async_search_or_index_client is None. Depending on the client type passed in, sync or async functions may not work.
Applying transformations: 100%|███████████████████| 1/1 [00:00<00:00, 58.39it/s]
Generating embeddings:   0%|                             | 0/34 [00:00<?, ?it/s]Traceback (most recent call last):
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
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\core\indices\vector_store\base.py", line 231, in _add_nodes_to_index
    nodes_batch = self._get_node_with_embedding(nodes_batch, show_progress)      
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\core\indices\vector_store\base.py", line 138, in _get_node_with_embedding
    id_to_embed_map = embed_nodes(
        nodes, self._embed_model, show_progress=show_progress
    )
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\core\indices\utils.py", line 177, in embed_nodes
    new_embeddings = embed_model.get_text_embedding_batch(
        texts_to_embed, show_progress=show_progress
    )
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index_instrumentation\dispatcher.py", line 335, in wrapper
    result = func(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\core\base\embeddings\base.py", line 476, in get_text_embedding_batch
    embeddings = self._get_text_embeddings(cur_batch)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\embeddings\openai\base.py", line 472, in _get_text_embeddings
    return _retryable_get_embeddings()
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\tenacity\__init__.py", line 331, in wrapped_f
    return copy(f, *args, **kw)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\tenacity\__init__.py", line 470, in __call__
    do = self.iter(retry_state=retry_state)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\tenacity\__init__.py", line 371, in iter
    result = action(retry_state)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\tenacity\__init__.py", line 393, in <lambda>
    self._add_action_func(lambda rs: rs.outcome.result())
                                     ~~~~~~~~~~~~~~~~~^^
  File "C:\Users\h84609n\AppData\Local\Programs\Python\Python314\Lib\concurrent\futures\_base.py", line 443, in result
    return self.__get_result()
           ~~~~~~~~~~~~~~~~~^^
  File "C:\Users\h84609n\AppData\Local\Programs\Python\Python314\Lib\concurrent\futures\_base.py", line 395, in __get_result
    raise self._exception
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\tenacity\__init__.py", line 473, in __call__
    result = fn(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\embeddings\openai\base.py", line 465, in _retryable_get_embeddings
    return get_embeddings(
        client,
    ...<2 lines>...
        **self.additional_kwargs,
    )
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\llama_index\embeddings\openai\base.py", line 172, in get_embeddings
    data = client.embeddings.create(input=list_of_text, model=engine, **kwargs).data
           ~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\openai\resources\embeddings.py", line 132, in create
    return self._post(
           ~~~~~~~~~~^
        "/embeddings",
        ^^^^^^^^^^^^^^
    ...<8 lines>...
        cast_to=CreateEmbeddingResponse,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\openai\_base_client.py", line 1297, in post
    return cast(ResponseT, self.request(cast_to, opts, stream=stream, stream_cls=stream_cls))
                           ~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\openai\_base_client.py", line 1070, in request
    raise self._make_status_error_from_response(err.response) from None
openai.NotFoundError: Error code: 404 - {'error': {'code': 'DeploymentNotFound', 
'message': 'The API deployment for this resource does not exist. If you created the deployment within the last 5 minutes, please wait a moment and try again.'}}  
(.venv) PS C:\Users\h84609n\Desktop\Embedding> 
