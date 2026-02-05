(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> py test_rag.py
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 32, in <module>
    results = retriever.retrieve(user_story, description, ac)
  File "C:\Users\h84609n\Desktop\VectorDb Test\rag_query.py", line 70, in retrieve
    query_vector = self.embed_query(query_text)
  File "C:\Users\h84609n\Desktop\VectorDb Test\rag_query.py", line 58, in embed_query      
    emb = self.openai.embeddings.create(
        model=self.embed_model,
        input=text
    )
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\openai\resources\embeddings.py", line 132, in create
    return self._post(
           ~~~~~~~~~~^
        "/embeddings",
        ^^^^^^^^^^^^^^
    ...<8 lines>...
        cast_to=CreateEmbeddingResponse,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\openai\_base_client.py", line 1294, in post
    return cast(ResponseT, self.request(cast_to, opts, stream=stream, stream_cls=stream_cls))
                           ~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\openai\_base_client.py", line 1067, in request
    raise self._make_status_error_from_response(err.response) from None
openai.NotFoundError: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not 
found'}}
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> 
