(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> py test_rag.py
🔎 Channels from AC: ['WHL']
value is not a known attribute of class <class 'azure.search.documents._generated.models._models_py3.VectorQuery'> and will be ignored
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 32, in <module>
    results = retriever.retrieve(user_story, description, ac)
  File "C:\Users\h84609n\Desktop\VectorDb Test\rag_query.py", line 98, in retrieve
    for r in results:
             ^^^^^^^
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
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\azure\search\documents\_generated\operations\_documents_operations.py", line 949, in search_post
    raise HttpResponseError(response=response, model=error)
azure.core.exceptions.HttpResponseError: (InvalidRequestParameter) The vector query's 'kind' parameter is not set.
Parameter name: vector.kind
Code: InvalidRequestParameter
Message: The vector query's 'kind' parameter is not set.
Parameter name: vector.kind
Exception Details:      (InvalidVectorQuery) The vector query's 'kind' parameter is not set.
        Code: InvalidVectorQuery
        Message: The vector query's 'kind' parameter is not set.
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> 
