🔎 Hybrid search for channel: WHL
2026-02-09 22:44:28,556 - httpx - INFO - HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
2026-02-09 22:44:28,627 - azure.core.pipeline.policies.http_logging_policy - INFO - Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('testcase-vectordb-test6')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '75576'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'caa8d2ec-05da-11f1-a72c-7ced8dc28672'
    'User-Agent': 'azsdk-python-search-documents/11.7.0b2 Python/3.14.2 (Windows-10-10.0.19045-SP0)'
A body is sent with the request
2026-02-09 22:44:29,931 - azure.core.pipeline.policies.http_logging_policy - INFO - Response status: 400
Response headers:
    'Cache-Control': 'no-cache,no-store'
    'Pragma': 'no-cache'
    'Content-Length': '191'
    'Content-Type': 'application/json; charset=utf-8'
    'Content-Language': 'REDACTED'
    'Expires': '-1'
    'request-id': 'caa8d2ec-05da-11f1-a72c-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Strict-Transport-Security': 'REDACTED'
    'Date': 'Mon, 09 Feb 2026 17:14:29 GMT'
2026-02-09 22:44:29,932 - test_rag_runner - ERROR - ERROR OCCURRED IN RAG PIPELINE
Traceback (most recent call last):
  File "c:\Users\h84609n\Desktop\VectorDb Test\adomcpserver\test_rag_runner.py", line 33, in run_rag_pipeline
    results = retriever.retrieve_for_channel(
        user_story,
    ...<2 lines>...
        channel
    )
  File "c:\Users\h84609n\Desktop\VectorDb Test\ContextRetrieval_ReRanking\ragquery\rag_query.py", line 92, in retrieve_for_channel
    results_list = list(results)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\search\documents\_paging.py", line 58, in __next__
    return next(self._page_iterator)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\paging.py", line 82, in __next__
    self._response = self._get_next(self.continuation_token)
                     ~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\search\documents\_paging.py", line 139, in _get_next_cb
    return self._client.documents.search_post(search_request=self._initial_query.request, **self._kwargs)
           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\tracing\decorator.py", line 119, in wrapper_use_tracer
    return func(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\search\documents\_generated\operations\_documents_operations.py", line 865, in search_post
    raise HttpResponseError(response=response, model=error)
azure.core.exceptions.HttpResponseError: () This index must have valid semantic configurations defined before using the 'semanticConfiguration' query parameter.

Parameter name: semanticConfiguration
Code: 
Message: This index must have valid semantic configurations defined before using the 'semanticConfiguration' query parameter.

Parameter name: semanticConfiguration
2026-02-09 22:44:29,936 - __main__ - ERROR - Error during test case generation
Traceback (most recent call last):
  File "c:\Users\h84609n\Desktop\VectorDb Test\adomcpserver\server.py", line 103, in us_TestcaseGenerator
    output_excel = run_rag_pipeline(
        user_story_id=user_story_id,
    ...<2 lines>...
        ac=clean_ac,
    )
  File "c:\Users\h84609n\Desktop\VectorDb Test\adomcpserver\test_rag_runner.py", line 33, in run_rag_pipeline
    results = retriever.retrieve_for_channel(
        user_story,
    ...<2 lines>...
        channel
    )
  File "c:\Users\h84609n\Desktop\VectorDb Test\ContextRetrieval_ReRanking\ragquery\rag_query.py", line 92, in retrieve_for_channel
    results_list = list(results)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\search\documents\_paging.py", line 58, in __next__
    return next(self._page_iterator)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\paging.py", line 82, in __next__
    self._response = self._get_next(self.continuation_token)
                     ~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\search\documents\_paging.py", line 139, in _get_next_cb
    return self._client.documents.search_post(search_request=self._initial_query.request, **self._kwargs)
           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\tracing\decorator.py", line 119, in wrapper_use_tracer
    return func(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\search\documents\_generated\operations\_documents_operations.py", line 865, in search_post
    raise HttpResponseError(response=response, model=error)
azure.core.exceptions.HttpResponseError: () This index must have valid semantic configurations defined before using the 'semanticConfiguration' query parameter.

Parameter name: semanticConfiguration
Code: 
Message: This index must have valid semantic configurations defined before using the 'semanticConfiguration' query parameter.

Parameter name: semanticConfiguration
