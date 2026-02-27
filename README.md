INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.ado_intelligence_agent:✅ GPT-4o response received
ERROR:utils.step_generartor:❌ JSON parsing failed: Expecting value: line 1 column 1 (char 0)
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
  File "C:\Users\h84609n\Desktop\AgenticAI\agents\ado_intelligence_agent.py", line 107, in run
    save_final_txt(
    ~~~~~~~~~~~~~~^
        story_id,
        ^^^^^^^^^
    ...<3 lines>...
        grouped_steps
        ^^^^^^^^^^^^^
    )
    ^
TypeError: save_final_txt() takes 4 positional arguments but 5 were given
During task with name 'ado_agent' and id '85119e4f-0038-b016-6c19-f9afe7c88804'
