    'Date': 'Mon, 23 Feb 2026 13:35:00 GMT'
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\AgenticAI\run_agent.py", line 24, in <module>
    run("718521")
    ~~~^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\AgenticAI\run_agent.py", line 16, in run
    final_state = app.invoke(initial_state)
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langgraph\pregel\main.py", line 3071, in invoke        
    for chunk in self.stream(
                 ~~~~~~~~~~~^
        input,
        ^^^^^^
    ...<10 lines>...
        **kwargs,
        ^^^^^^^^^
    ):
    ^
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langgraph\pregel\main.py", line 2646, in stream        
    for _ in runner.tick(
             ~~~~~~~~~~~^
        [t for t in loop.tasks.values() if not t.writes],
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    ...<2 lines>...
        schedule_task=loop.accept_push,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    ):
    ^
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langgraph\pregel\_runner.py", line 167, in tick        
    run_with_retry(
    ~~~~~~~~~~~~~~^
        t,
        ^^
    ...<10 lines>...
        },
        ^^
    )
    ^
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langgraph\pregel\_retry.py", line 42, in run_with_retry    return task.proc.invoke(task.input, config)
           ~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langgraph\_internal\_runnable.py", line 656, in invoke 
    input = context.run(step.invoke, input, config, **kwargs)
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langgraph\_internal\_runnable.py", line 400, in invoke 
    ret = self.func(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\AgenticAI\agents\retrieval_intelligence_agent.py", line 205, in run
    retrieved_docs[channel] = self._build_channel_context(full_story, channel)
                              ~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\AgenticAI\agents\retrieval_intelligence_agent.py", line 176, in _build_channel_context   
    tests = self._vector_retrieve(channel_text, channel, 40)
  File "C:\Users\h84609n\Desktop\AgenticAI\agents\retrieval_intelligence_agent.py", line 57, in _vector_retrieve
    results = list(self.search_client.search(
        search_text=query_text,
    ...<3 lines>...
        top=topk
    ))
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\azure\search\documents\_paging.py", line 54, in __next__
    return next(self._page_iterator)
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\azure\core\paging.py", line 82, in __next__
    self._response = self._get_next(self.continuation_token)
                     ~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\azure\search\documents\_paging.py", line 131, in _get_next_cb
    return self._client.documents.search_post(
           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^
        search_request=self._initial_query.request, **self._kwargs
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\azure\core\tracing\decorator.py", line 119, in wrapper_use_tracer
    return func(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\azure\search\documents\_generated\operations\_documents_operations.py", line 949, in search_post
    raise HttpResponseError(response=response, model=error)
azure.core.exceptions.HttpResponseError: () Invalid expression: Could not find a property named 'knowledgeType' on type 'search.document'.
Parameter name: $filter
Code:
Message: Invalid expression: Could not find a property named 'knowledgeType' on type 'search.document'.
Parameter name: $filter
During task with name 'retrieval_agent' and id 'faa98ca5-4d8c-07ba-e1d4-3ad2fe47799c'
(.venv) PS C:\Users\h84609n\Desktop\AgenticAI> 
