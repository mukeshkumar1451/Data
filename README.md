C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langchain_core\_api\deprecation.py:25: UserWarning: Core Pydantic V1 functionality isn't compatible with Python 3.14 or greater.
  from pydantic.v1.fields import FieldInfo as FieldInfoV1
INFO:agents.llm_testcase_generator_agent:✅ LLM Testcase Generator initialized
INFO:agents.ado_intelligence_agent:🚀 ADO Intelligence Agent started
OCR DEBUG: Extracted text length = 301
OCR DEBUG: Extracted text length = 44
OCR DEBUG: Extracted text length = 614
OCR DEBUG: Extracted text length = 543
INFO:utils.channel_detector:Behavioral channel detection started...
INFO:utils.channel_detector:No strong signal → using ALL channels
INFO:agents.ado_intelligence_agent:🧠 Extracting derived UI rules
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

=========== ADO INTELLIGENCE AGENT OUTPUT ===========

User Story ID: 718521
TITLE: Modernized Audit additions - DIS > Generate Disclosures Fields


=========== DERIVED UI RULES ===========

Rule 1:
IF SubPropState = CA
THEN Mortgage Broker License Type field becomes visible

Rule 2:
Mortgage Broker License Type controls visibility of Mortgage Broker Fee/Compensation Agreement      

Rule 3:
Privilege restrictions override visibility of Mortgage Broker Fee/Compensation Agreement

Rule 4:
Privilege restrictions override visibility of Mortgage Broker License Type

=====================================================


 State dumped to: debug\ado_agent_unknown_output.txt

INFO:agents.ado_intelligence_agent:✅ ADO Intelligence Agent completed

 Excel Generated at:
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\AgenticAI\run_agent.py", line 24, in <module>
    run("718521")
    ~~~^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\AgenticAI\run_agent.py", line 19, in run
    print(final_state["excel_output"])
          ~~~~~~~~~~~^^^^^^^^^^^^^^^^
KeyError: 'excel_output'
(.venv) PS C:\Users\h84609n\Desktop\AgenticAI> py run_agent.py
C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langchain_core\_api\deprecation.py:25: UserWarning: Core Pydantic V1 functionality isn't compatible with Python 3.14 or greater.
  from pydantic.v1.fields import FieldInfo as FieldInfoV1
INFO:agents.llm_testcase_generator_agent:✅ LLM Testcase Generator initialized
INFO:agents.ado_intelligence_agent:🚀 ADO Intelligence Agent started
OCR DEBUG: Extracted text length = 301
OCR DEBUG: Extracted text length = 44
OCR DEBUG: Extracted text length = 614
OCR DEBUG: Extracted text length = 543
INFO:utils.channel_detector:Behavioral channel detection started...
INFO:utils.channel_detector:No strong signal → using ALL channels
INFO:agents.ado_intelligence_agent:🧠 Extracting derived UI rules
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

=========== ADO INTELLIGENCE AGENT OUTPUT ===========

User Story ID: 718521
TITLE: Modernized Audit additions - DIS > Generate Disclosures Fields


=========== DERIVED UI RULES ===========

Rule 1:
IF SubPropState = CA
THEN Mortgage Broker License Type field becomes visible

Rule 2:
Mortgage Broker License Type controls visibility of Mortgage Broker Fee/Compensation Agreement      

Rule 3:
Privilege restrictions override visibility of Mortgage Broker Fee/Compensation Agreement

Rule 4:
Privilege restrictions override visibility of Mortgage Broker License Type

=====================================================


 State dumped to: debug\ado_agent_unknown_output.txt

INFO:agents.ado_intelligence_agent:✅ ADO Intelligence Agent completed
INFO:agents.retrieval_intelligence_agent:🚀 Retrieval Agent Running
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
  File "C:\Users\h84609n\Desktop\AgenticAI\agents\retrieval_intelligence_agent.py", line 159, in run    User Story: {state['user_story']}
                 ~~~~~^^^^^^^^^^^^^^
KeyError: 'user_story'
During task with name 'retrieval_agent' and id 'e04ddac9-19ba-34ba-067b-5673a3c9d0f7'
