INFO:agents.llm_testcase_generator_agent:🤖 LLM Generator Running
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
  File "C:\Users\h84609n\Desktop\AgenticAI\agents\llm_testcase_generator_agent.py", line 74, in run
    result = self.chain.invoke(payload)
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langchain_core\runnables\base.py", line 3155, in invoke
    input_ = context.run(step.invoke, input_, config, **kwargs)
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langchain_core\prompts\base.py", line 223, in invoke
    return self._call_with_config(
           ~~~~~~~~~~~~~~~~~~~~~~^
        self._format_prompt_with_error_handling,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    ...<3 lines>...
        serialized=self._serialized,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langchain_core\runnables\base.py", line 2060, in _call_with_config
    context.run(
    ~~~~~~~~~~~^
        call_func_with_variable_args,  # type: ignore[arg-type]
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    ...<4 lines>...
        **kwargs,
        ^^^^^^^^^
    ),
    ^
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langchain_core\runnables\config.py", line 452, in 
call_func_with_variable_args
    return func(input, **kwargs)  # type: ignore[call-arg]
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langchain_core\prompts\base.py", line 196, in _format_prompt_with_error_handling
    inner_input_ = self._validate_input(inner_input)
  File "C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langchain_core\prompts\base.py", line 190, in _validate_input
    raise KeyError(
        create_message(message=msg, error_code=ErrorCode.INVALID_PROMPT_INPUT)
    )
KeyError: "Input to PromptTemplate is missing variables {'ModuleName'}.  Expected: ['ModuleName', 'ac', 'channel', 'description', 'historical_steps', 'precondition', 'user_story', 'user_story_id'] Received: ['user_story_id', 'user_story', 'description', 'ac', 'channel', 'precondition', 'historical_steps']\nNote: if you intended {ModuleName} to be part of the string and not a variable, please escape it with double curly braces like: '{{ModuleName}}'.\nFor troubleshooting, visit: https://docs.langchain.com/oss/python/langchain/errors/INVALID_PROMPT_INPUT "
During task with name 'llm_agent' and id '5c7e4cae-372e-0d05-30ba-5b0d15e506f8'
(.venv) PS C:\Users\h84609n\Desktop\AgenticAI> 
