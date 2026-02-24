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
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langgraph\pregel\_retry.py", line 42, in run_with_retry
    return task.proc.invoke(task.input, config)
           ~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langgraph\_internal\_runnable.py", line 656, in invoke
    input = context.run(step.invoke, input, config, **kwargs)
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langgraph\_internal\_runnable.py", line 400, in invoke
    ret = self.func(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\AgenticAI\agents\excel_export_agent.py", line 99, in run
    precondition = state["selected_preconditions"].get(channel, "")
                   ~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^
KeyError: 'selected_preconditions'
During task with name 'excel_agent' and id '464df063-34ce-99f2-14b3-b529707c15c7'
(.venv) PS C:\Users\h84609n\Desktop\AgenticAI> 
