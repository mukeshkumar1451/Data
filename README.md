Total files found: 27
Already processed: 0


Processing: data/excels\Indiv_US_36164_Test Scripts_v1.0.xlsx
Scanning file: data/excels\Indiv_US_36164_Test Scripts_v1.0.xlsx
  Processing sheet 'DTC'
  Processing sheet 'RTL'
  Processing sheet 'WHL'
  Processing sheet 'CL1'
Extracted 34 steps from data/excels\Indiv_US_36164_Test Scripts_v1.0.xlsx        

Uploading 34 steps to vector DB...
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\Embedding\main_ingest.py", line 30, in <module>
    build_index(docs)
    ~~~~~~~~~~~^^^^^^
  File "C:\Users\h84609n\Desktop\Embedding\ingestion\build_index.py", line 15, in build_index
    ensure_index()
    ~~~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\Embedding\ingestion\ensure_index.py", line 24, in ensure_index
    existing = [i.name for i in client.list_indexes()]
                                ~~~~~~~~~~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\paging.py", line 136, in __next__
    return next(self._page_iterator)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\paging.py", line 82, in __next__
    self._response = self._get_next(self.continuation_token)
                     ~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\search\documents\indexes\_generated\operations\_indexes_operations.py", line 492, in get_next
    pipeline_response: PipelineResponse = self._client._pipeline.run(  # pylint: 
disable=protected-access
                                          ~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        _request, stream=_stream, **kwargs
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 242, in run
    return first_node.send(pipeline_request)
           ~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 98, in send
    response = self.next.send(request)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 98, in send
    response = self.next.send(request)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 98, in send
    response = self.next.send(request)
  [Previous line repeated 2 more times]
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\pipeline\policies\_redirect.py", line 205, in send
    response = self.next.send(request)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\pipeline\policies\_retry.py", line 567, in send
    raise err
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\pipeline\policies\_retry.py", line 545, in send
    response = self.next.send(request)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 98, in send
    response = self.next.send(request)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 98, in send
    response = self.next.send(request)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 98, in send
    response = self.next.send(request)
  [Previous line repeated 2 more times]
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 130, in send
    self._sender.send(request.http_request, **request.context.options),
    ~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\azure\core\pipeline\transport\_requests_basic.py", line 422, in send
    raise error
azure.core.exceptions.ServiceRequestError: HTTPSConnection(host='https', port=443): Failed to resolve 'https' ([Errno 11001] getaddrinfo failed)
(.venv) PS C:\Users\h84609n\Desktop\Embedding> 
