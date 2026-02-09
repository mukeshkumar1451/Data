2026-02-09 22:52:19,699 - httpx - INFO - HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
2026-02-09 22:52:19,700 - ContextRetrieval_ReRanking.ragquery.rag_query - INFO - ✅ LLM response received for WHL

2026-02-09 22:52:19,700 - test_rag_runner - ERROR - ERROR OCCURRED IN RAG PIPELINE
Traceback (most recent call last):
  File "c:\Users\h84609n\Desktop\VectorDb Test\adomcpserver\test_rag_runner.py", line 48, in run_rag_pipeline
    parsed = parse_llm_steps(llm_outputs[channel])
                             ~~~~~~~~~~~^^^^^^^^^
KeyError: 'CL1'
2026-02-09 22:52:19,702 - __main__ - ERROR - Error during test case generation
Traceback (most recent call last):
  File "c:\Users\h84609n\Desktop\VectorDb Test\adomcpserver\server.py", line 103, in us_TestcaseGenerator
    output_excel = run_rag_pipeline(
        user_story_id=user_story_id,
    ...<2 lines>...
        ac=clean_ac,
    )
  File "c:\Users\h84609n\Desktop\VectorDb Test\adomcpserver\test_rag_runner.py", line 48, in run_rag_pipeline
    parsed = parse_llm_steps(llm_outputs[channel])
                             ~~~~~~~~~~~^^^^^^^^^
KeyError: 'CL1'
