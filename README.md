  from pydantic.v1.fields import FieldInfo as FieldInfoV1
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\AgenticAI\run_agent.py", line 24, in <module>
    run("718521")
    ~~~^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\AgenticAI\run_agent.py", line 11, in run
    app = build_graph()
  File "C:\Users\h84609n\Desktop\AgenticAI\graph\graph_builder.py", line 19, in build_graph
    ado_agent = ADOIntelligenceAgent()
TypeError: ADOIntelligenceAgent.__init__() missing 2 required positional arguments: 'openai_client' and 'model'      
(.venv) PS C:\Users\h84609n\Desktop\AgenticAI> 
