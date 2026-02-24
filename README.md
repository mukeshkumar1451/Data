(.venv) PS C:\Users\h84609n\Desktop\AgenticAI> py run_agent.py
C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langchain_core\_api\deprecation.py:25: UserWarning: Core Pydantic V1 functionality isn't compatible with Python 3.14 or greater.
  from pydantic.v1.fields import FieldInfo as FieldInfoV1
INFO:agents.llm_testcase_generator_agent:✅ LLM Testcase Generator initialized
INFO:agents.ado_intelligence_agent:🚀 ADO Intelligence Agent started
INFO:agents.ado_intelligence_agent:🧹 Processing Description...
OCR DEBUG: Extracted text length = 301
OCR DEBUG: Extracted text length = 44
OCR DEBUG: Extracted text length = 614
OCR DEBUG: Extracted text length = 543
INFO:agents.ado_intelligence_agent:🧹 Processing Acceptance Criteria...
OCR DEBUG: Extracted text length = 301
OCR DEBUG: Extracted text length = 44
OCR DEBUG: Extracted text length = 614
OCR DEBUG: Extracted text length = 543
INFO:utils.channel_detector:Behavioral channel detection started...
INFO:utils.channel_detector:No strong signal → using ALL channels
INFO:agents.ado_intelligence_agent:✅ Channels detected: ['RTL', 'WHL', 'DTC', 'CL1']
INFO:agents.ado_intelligence_agent:🧠 Deriving channel specific flows
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
WARNING:agents.ado_intelligence_agent:Channel derivation failed: Expecting value: line 1 column 1 (char 0)
INFO:agents.ado_intelligence_agent:📄 Generating structured business summary
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

=========== ADO INTELLIGENCE AGENT OUTPUT ===========

User Story ID: 718521
TITLE: Modernized Audit additions - DIS > Generate Disclosures Fields

=====================================================


 State dumped to: debug\ado_agent_718521_output.txt

INFO:agents.ado_intelligence_agent:✅ ADO Intelligence Agent completed successfully
INFO:agents.retrieval_intelligence_agent:🚀 Retrieval Agent Running
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '73929'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '75def7c5-11ae-11f1-baec-7ced8dc28672'
    'User-Agent': 'azsdk-python-search-documents/11.6.0 Python/3.14.2 (Windows-10-10.0.19045-SP0)'  
A body is sent with the request
INFO:azure.core.pipeline.policies.http_logging_policy:Response status: 200
Response headers:
    'Transfer-Encoding': 'chunked'
    'Content-Type': 'application/json; odata.metadata=none; odata.streaming=true; charset=utf-8'    
    'Content-Encoding': 'REDACTED'
    'Vary': 'REDACTED'
    'Strict-Transport-Security': 'REDACTED'
    'Preference-Applied': 'REDACTED'
    'OData-Version': 'REDACTED'
    'request-id': '75def7c5-11ae-11f1-baec-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 18:27:22 GMT'
INFO:agents.retrieval_intelligence_agent:RTL → Retrieved 19 docs
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '73929'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '77aff5f1-11ae-11f1-9cfc-7ced8dc28672'
    'User-Agent': 'azsdk-python-search-documents/11.6.0 Python/3.14.2 (Windows-10-10.0.19045-SP0)'  
A body is sent with the request
INFO:azure.core.pipeline.policies.http_logging_policy:Response status: 200
Response headers:
    'Transfer-Encoding': 'chunked'
    'Content-Type': 'application/json; odata.metadata=none; odata.streaming=true; charset=utf-8'    
    'Content-Encoding': 'REDACTED'
    'Vary': 'REDACTED'
    'Strict-Transport-Security': 'REDACTED'
    'Preference-Applied': 'REDACTED'
    'OData-Version': 'REDACTED'
    'request-id': '77aff5f1-11ae-11f1-9cfc-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 18:27:25 GMT'
INFO:agents.retrieval_intelligence_agent:WHL → Retrieved 20 docs
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '73929'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '78ba7894-11ae-11f1-980f-7ced8dc28672'
    'User-Agent': 'azsdk-python-search-documents/11.6.0 Python/3.14.2 (Windows-10-10.0.19045-SP0)'  
A body is sent with the request
INFO:azure.core.pipeline.policies.http_logging_policy:Response status: 200
Response headers:
    'Transfer-Encoding': 'chunked'
    'Content-Type': 'application/json; odata.metadata=none; odata.streaming=true; charset=utf-8'    
    'Content-Encoding': 'REDACTED'
    'Vary': 'REDACTED'
    'Strict-Transport-Security': 'REDACTED'
    'Preference-Applied': 'REDACTED'
    'OData-Version': 'REDACTED'
    'request-id': '78ba7894-11ae-11f1-980f-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 18:27:27 GMT'
INFO:agents.retrieval_intelligence_agent:DTC → Retrieved 18 docs
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '73929'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '79d361f3-11ae-11f1-bffa-7ced8dc28672'
    'User-Agent': 'azsdk-python-search-documents/11.6.0 Python/3.14.2 (Windows-10-10.0.19045-SP0)'  
A body is sent with the request
INFO:azure.core.pipeline.policies.http_logging_policy:Response status: 200
Response headers:
    'Transfer-Encoding': 'chunked'
    'Content-Type': 'application/json; odata.metadata=none; odata.streaming=true; charset=utf-8'    
    'Content-Encoding': 'REDACTED'
    'Vary': 'REDACTED'
    'Strict-Transport-Security': 'REDACTED'
    'Preference-Applied': 'REDACTED'
    'OData-Version': 'REDACTED'
    'request-id': '79d361f3-11ae-11f1-bffa-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 18:27:28 GMT'
INFO:agents.retrieval_intelligence_agent:CL1 → Retrieved 20 docs
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:CL1 → Selected Precondition:
Pre-Condition & Assumptions:
Create a loan from BP:
1. Channel: CL1
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: Any
5. Loan stage should be UW Submitted
6. Early Disclosure should be generated, sent via eSign ,Esign should be completed and disclosure should be received.

=========== TEST STEPS ===========

INFO:agents.retrieval_intelligence_agent:✅ Retrieval Completed
INFO:agents.llm_testcase_generator_agent:🤖 LLM Generator Running
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.llm_testcase_generator_agent:✅ LLM Generation Completed
INFO:agents.excel_export_agent:Excel Export Agent started
INFO:agents.excel_export_agent:RTL -> Parsed 14 steps
Selected Precondition:
None
INFO:agents.excel_export_agent:WHL -> Parsed 12 steps
Selected Precondition:
None
INFO:agents.excel_export_agent:DTC -> Parsed 14 steps
Selected Precondition:
None
INFO:agents.excel_export_agent:CL1 -> Parsed 10 steps
Selected Precondition:
None
INFO:agents.excel_export_agent:Excel generated: output_excels\Indiv_US_718521_Test_Scripts_v1.0.xlsx

 Excel Generated at:
output_excels\Indiv_US_718521_Test_Scripts_v1.0.xlsx
(.venv) PS C:\Users\h84609n\Desktop\AgenticAI>
















