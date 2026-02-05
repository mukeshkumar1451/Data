PS C:\Users\h84609n\Desktop\VectorDb Test> py test_rag.py
🔎 Channels from AC: ['WHL']
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 32, in <module>
    results = retriever.retrieve(user_story, description, ac)
  File "C:\Users\h84609n\Desktop\VectorDb Test\rag_query.py", line 90, in retrieve
    return list(results)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\search\documents\_paging.py", line 54, in __next__
    return next(self._page_iterator)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\paging.py", line 82, in __next__
    self._response = self._get_next(self.continuation_token)
                     ~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\search\documents\_paging.py", line 131, in _get_next_cb
    return self._client.documents.search_post(
           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^
        search_request=self._initial_query.request, **self._kwargs
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\tracing\decorator.py", line 119, in wrapper_use_tracer
    return func(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\search\documents\_generated\operations\_documents_operations.py", line 935, in search_post
    self._client._pipeline.run(  # pylint: disable=protected-access
    ~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        _request, stream=_stream, **kwargs
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 242, in run
    return first_node.send(pipeline_request)
           ~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 98, in send
    response = self.next.send(request)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 98, in send
    response = self.next.send(request)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 98, in send
    response = self.next.send(request)
  [Previous line repeated 2 more times]
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\pipeline\policies\_redirect.py", line 205, in send
    response = self.next.send(request)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\pipeline\policies\_retry.py", line 545, in send
    response = self.next.send(request)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 98, in send
    response = self.next.send(request)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 98, in send
    response = self.next.send(request)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 98, in send
    response = self.next.send(request)
  [Previous line repeated 2 more times]
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\pipeline\_base.py", line 130, in send
    self._sender.send(request.http_request, **request.context.options),
    ~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\core\pipeline\transport\_requests_basic.py", line 375, in send
    response = self.session.request(  # type: ignore
        request.method,
    ...<9 lines>...
        **kwargs
    )
TypeError: Session.request() got an unexpected keyword argument 'vector'
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> 
