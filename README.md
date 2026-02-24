C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langchain_core\_api\deprecation.py:25: UserWarning: Core Pydantic V1 functionality isn't compatible with Python 3.14 or greater.
  from pydantic.v1.fields import FieldInfo as FieldInfoV1
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
INFO:agents.retrieval_intelligence_agent:🚀 Retrieval Intelligence Agent Running
INFO:agents.retrieval_intelligence_agent:🔎 Hybrid search for channel: RTL
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '73929'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '231181ed-119b-11f1-95cc-7ced8dc28672'
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
    'request-id': '231181ed-119b-11f1-95cc-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 16:09:04 GMT'
INFO:agents.retrieval_intelligence_agent:📊 Retrieved 19 docs for RTL
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:🔎 Hybrid search for channel: WHL
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '73929'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '24eb33c7-119b-11f1-b0f5-7ced8dc28672'
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
    'request-id': '24eb33c7-119b-11f1-b0f5-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 16:09:06 GMT'
INFO:agents.retrieval_intelligence_agent:📊 Retrieved 30 docs for WHL
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:🔎 Hybrid search for channel: DTC
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '73929'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '26610a39-119b-11f1-b8ad-7ced8dc28672'
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
    'request-id': '26610a39-119b-11f1-b8ad-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 16:09:08 GMT'
INFO:agents.retrieval_intelligence_agent:📊 Retrieved 18 docs for DTC
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:🔎 Hybrid search for channel: CL1
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '73929'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '2796c262-119b-11f1-9015-7ced8dc28672'
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
    'request-id': '2796c262-119b-11f1-9015-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 16:09:10 GMT'
INFO:agents.retrieval_intelligence_agent:📊 Retrieved 21 docs for CL1
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:✅ Retrieval Completed
INFO:agents.llm_testcase_generator_agent:🚀 LLM Generator Running
INFO:agents.llm_testcase_generator_agent:🤖 Generating testcase for channel: RTL
INFO:agents.llm_testcase_generator_agent:📄 Prompt written to: debug/llm_prompt_718521_RTL.txt
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

Scenario: Validate Modernized Audit additions for Generate Disclosures Fields
Script: Generate Disclosures Field Validation
Requirement: US-12345

Step 01 | Navigate to Generate Disclosures section | DIS > Generate Disclosures | Loan with SubPropState = CA | Verify Mortgage Broker License Type field appears
Step 02 | Select Intent to Proceed option | DIS > Generate Disclosures | Intent to Proceed = Yes | Verify consent to 
receive disclosures electronically is captured
Step 03 | Include Mortgage Broker Fee Agreement | DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement | Agreement = Yes | Verify agreement is appended to Newrez LE Package
INFO:agents.llm_testcase_generator_agent:🤖 Generating testcase for channel: WHL
INFO:agents.llm_testcase_generator_agent:📄 Prompt written to: debug/llm_prompt_718521_WHL.txt
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

Scenario: Validate the addition of new fields in Modernized Audit for Generate Disclosures
Script: Generate Disclosures Field Validation
Requirement: US-12345

Step 01 | Navigate to Generate Disclosures section | Broker Portal | Loan with SubPropState = CA | Verify Mortgage Broker License Type field appears
Step 02 | Select Yes for Mortgage Broker Fee/Compensation Agreement | Generate Disclosures | Loan with privilege access | Verify field is privilege restricted
Step 03 | Submit Generate Disclosures | Generate Disclosures | Loan with HPML selected | Verify Intent to Proceed field is displayed
INFO:agents.llm_testcase_generator_agent:🤖 Generating testcase for channel: DTC
INFO:agents.llm_testcase_generator_agent:📄 Prompt written to: debug/llm_prompt_718521_DTC.txt
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

Scenario: Validate the addition of new fields in Modernized Audit for Generate Disclosures
Script: Modernized Audit - Generate Disclosures Fields
Requirement: US-12345

Step 01 | Login to the Ignite Portal | Login Screen | Valid credentials | User is successfully logged into the Ignite Portal
Step 02 | Navigate to Generate Disclosures section | Main Menu | Select Generate Disclosures | Generate Disclosures screen is displayed
Step 03 | Verify the presence of new fields | Generate Disclosures Screen | Fields: HPML, Intent to Proceed, Mortgage Broker Fee Agreement, Mortgage Broker License Type | All specified fields are displayed as per the acceptance criteria
INFO:agents.llm_testcase_generator_agent:🤖 Generating testcase for channel: CL1
INFO:agents.llm_testcase_generator_agent:📄 Prompt written to: debug/llm_prompt_718521_CL1.txt
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

Scenario: Validate the addition of new fields in Modernized Audit for Generate Disclosures
Script: Modernized Audit - Generate Disclosures Fields
Requirement: US12345

Step 01 | Navigate to Generate Disclosures screen | DIS > Generate Disclosures | N/A | Generate Disclosures screen is displayed
Step 02 | Verify the presence of HPML field | DIS > Generate Disclosures | N/A | HPML field is displayed as per requirements
Step 03 | Verify the presence of Intent to Proceed field | DIS > Generate Disclosures | N/A | Intent to Proceed field is displayed as per requirements
Step 04 | Verify the presence of Mortgage Broker Fee Agreement field | DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement | N/A | Mortgage Broker Fee Agreement field is displayed as per requirements
Step 05 | Verify the presence of Mortgage Broker License Type field | DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement | SubPropState = CA | Mortgage Broker License Type field is displayed as per requirements   
INFO:agents.llm_testcase_generator_agent:✅ LLM Generation Completed
INFO:agents.excel_export_agent:Excel Export Agent started
INFO:agents.excel_export_agent:RTL -> Parsed 3 steps
INFO:agents.excel_export_agent:WHL -> Parsed 3 steps
INFO:agents.excel_export_agent:DTC -> Parsed 3 steps
INFO:agents.excel_export_agent:CL1 -> Parsed 5 steps
INFO:agents.excel_export_agent:Excel generated: output_excels\Indiv_US_718521_Test Scripts_v1.0.xlsx

 Excel Generated at:
output_excels\Indiv_US_718521_Test Scripts_v1.0.xlsx
