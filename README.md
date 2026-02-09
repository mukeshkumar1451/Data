🔎 Hybrid search for channel: WHL
2026-02-09 18:09:33,059 - httpx - INFO - HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
2026-02-09 18:09:33,103 - azure.core.pipeline.policies.http_logging_policy - INFO - Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('testcase-vectordb-test5')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '75501'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '628fbca8-05b4-11f1-ae7a-7ced8dc28672'
    'User-Agent': 'azsdk-python-search-documents/11.7.0b2 Python/3.14.2 (Windows-10-10.0.19045-SP0)'
A body is sent with the request
2026-02-09 18:09:33,885 - azure.core.pipeline.policies.http_logging_policy - INFO - Response status: 400
Response headers:
    'Cache-Control': 'no-cache,no-store'
    'Pragma': 'no-cache'
    'Content-Length': '149'
    'Content-Type': 'application/json; charset=utf-8'
    'Content-Language': 'REDACTED'
    'Expires': '-1'
    'request-id': '628fbca8-05b4-11f1-ae7a-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Strict-Transport-Security': 'REDACTED'
    'Date': 'Mon, 09 Feb 2026 12:39:33 GMT'
2026-02-09 18:09:33,886 - test_rag_runner - ERROR - ERROR OCCURRED IN RAG PIPELINE
Traceback (most recent call last):
  File "c:\Users\h84609n\Desktop\VectorDb Test\adomcpserver\test_rag_runner.py", line 33, in run_rag_pipeline
    results = retriever.retrieve_for_channel(
        user_story,
    ...<2 lines>...
        channel
    )
  File "c:\Users\h84609n\Desktop\VectorDb Test\ContextRetrieval_ReRanking\ragquery\rag_query.py", line 85, in retrieve_for_channel
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
azure.core.exceptions.HttpResponseError: () Invalid expression: Could not find a property named 'channel' on type 'search.document'.

Parameter name: $filter
Code: 
Message: Invalid expression: Could not find a property named 'channel' on type 'search.document'.

Parameter name: $filter
2026-02-09 18:09:33,893 - __main__ - ERROR - Error during test case generation
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
  File "c:\Users\h84609n\Desktop\VectorDb Test\ContextRetrieval_ReRanking\ragquery\rag_query.py", line 85, in retrieve_for_channel
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
azure.core.exceptions.HttpResponseError: () Invalid expression: Could not find a property named 'channel' on type 'search.document'.

Parameter name: $filter
Code: 
Message: Invalid expression: Could not find a property named 'channel' on type 'search.document'.

Parameter name: $filter
2026-02-09 18:23:12,703 - mcp.server.lowlevel.server - INFO - Processing request of type CallToolRequest
2026-02-09 18:23:12,703 - __main__ - INFO - TEST CASE GENERATION STARTED FOR: 718521
2026-02-09 18:23:13,406 - utils.html_image_processor - INFO - 🧹 Cleaning Acceptance Criteria HTML...
2026-02-09 18:23:13,409 - utils.html_image_processor - INFO - 🗼️ Extracting images from AC...
2026-02-09 18:23:13,409 - test_rag_runner - INFO - 🚀 Channel-Aware RAG Test Case Generation Started
2026-02-09 18:23:13,409 - ContextRetrieval_ReRanking.channel_detect.channel_detector - INFO - 
🔎 Detecting channels from Acceptance Criteria...

2026-02-09 18:23:13,410 - ContextRetrieval_ReRanking.channel_detect.channel_detector - INFO - 🦬 Raw detected channels: set()
2026-02-09 18:23:13,410 - ContextRetrieval_ReRanking.channel_detect.channel_detector - INFO - ⚠️ No channel mentioned → Using ALL channels
2026-02-09 18:23:13,410 - test_rag_runner - INFO - Channels detected: ['WHL', 'RTL', 'DTC', 'CL1']
2026-02-09 18:23:14,046 - test_rag_runner - INFO - Processing Channel: WHL
2026-02-09 18:23:14,046 - ContextRetrieval_ReRanking.ragquery.rag_query - INFO - 
🔎 Hybrid search for channel: WHL
2026-02-09 18:23:14,363 - httpx - INFO - HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
2026-02-09 18:23:14,405 - azure.core.pipeline.policies.http_logging_policy - INFO - Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('testcase-vectordb-test5')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '75501'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '4c186844-05b6-11f1-9a64-7ced8dc28672'
    'User-Agent': 'azsdk-python-search-documents/11.7.0b2 Python/3.14.2 (Windows-10-10.0.19045-SP0)'
A body is sent with the request
2026-02-09 18:23:15,316 - azure.core.pipeline.policies.http_logging_policy - INFO - Response status: 400
Response headers:
    'Cache-Control': 'no-cache,no-store'
    'Pragma': 'no-cache'
    'Content-Length': '149'
    'Content-Type': 'application/json; charset=utf-8'
    'Content-Language': 'REDACTED'
    'Expires': '-1'
    'request-id': '4c186844-05b6-11f1-9a64-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Strict-Transport-Security': 'REDACTED'
    'Date': 'Mon, 09 Feb 2026 12:53:15 GMT'
2026-02-09 18:23:15,317 - test_rag_runner - ERROR - ERROR OCCURRED IN RAG PIPELINE
Traceback (most recent call last):
  File "c:\Users\h84609n\Desktop\VectorDb Test\adomcpserver\test_rag_runner.py", line 33, in run_rag_pipeline
    results = retriever.retrieve_for_channel(
        user_story,
    ...<2 lines>...
        channel
    )
  File "c:\Users\h84609n\Desktop\VectorDb Test\ContextRetrieval_ReRanking\ragquery\rag_query.py", line 85, in retrieve_for_channel
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
azure.core.exceptions.HttpResponseError: () Invalid expression: Could not find a property named 'channel' on type 'search.document'.

Parameter name: $filter
Code: 
Message: Invalid expression: Could not find a property named 'channel' on type 'search.document'.

Parameter name: $filter
2026-02-09 18:23:15,345 - __main__ - ERROR - Error during test case generation
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
  File "c:\Users\h84609n\Desktop\VectorDb Test\ContextRetrieval_ReRanking\ragquery\rag_query.py", line 85, in retrieve_for_channel
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
azure.core.exceptions.HttpResponseError: () Invalid expression: Could not find a property named 'channel' on type 'search.document'.

Parameter name: $filter
Code: 
Message: Invalid expression: Could not find a property named 'channel' on type 'search.document'.
