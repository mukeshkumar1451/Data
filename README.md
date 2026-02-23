INFO:agents.ado_intelligence_agent:🚀 ADO Intelligence Agent started
INFO:agents.ado_intelligence_agent:🧹 Processing Description HTML + Images...
INFO:agents.ado_intelligence_agent:🧹 Processing Acceptance Criteria HTML + Images...
INFO:utils.channel_detector:Behavioral channel detection started...
INFO:utils.channel_detector:No strong signal → using ALL channels
INFO:agents.ado_intelligence_agent:✅ Channels detected: ['RTL', 'WHL', 'DTC', 'CL1']
INFO:agents.ado_intelligence_agent:🧠 Deriving channel specific flows from story
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
WARNING:agents.ado_intelligence_agent:⚠️ LLM context split failed → fallback rule mode: Expecting value: line 1 column 1 (
char 0)

=========== ADO INTELLIGENCE AGENT OUTPUT ===========

User Story ID: 718521
TITLE: Modernized Audit additions - DIS > Generate Disclosures Fields

=====================================================


 State dumped to: debug\ado_agent_718521_output.txt

INFO:agents.retrieval_intelligence_agent:🚀 Retrieval Intelligence Agent (Stable Mode)
INFO:agents.retrieval_intelligence_agent:🔎 Building retrieval context for RTL
INFO:agents.retrieval_intelligence_agent:🔎 Running vector retrieval for RTL
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index1')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '70705'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '6f02e1eb-10b9-11f1-b30d-7ced8dc28672'
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
    'request-id': '6f02e1eb-10b9-11f1-b30d-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Mon, 23 Feb 2026 13:13:24 GMT'
INFO:agents.retrieval_intelligence_agent:📊 Channel RTL → Retrieved 19 documents
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 718524_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 738152_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 658373_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 718523_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 734893_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 718516_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 719229_RTL_02
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 718519_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 742620_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 738432_RTL_03
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 719229_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 733851_RTL_02
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 36164_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 733851_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 738432_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 738432_RTL_02
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 745767_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 739659_RTL_01
INFO:agents.retrieval_intelligence_agent:   ↳ RTL → 740712_Regression_RTL_01
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:✅ Reranked 1 testcases
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:
📌 Inferred Setup for RTL:
Loan Purpose: Purchase
Loan Type: Conventional
Product: Fixed Rate 30-Year
Loan Stage: Disclosure Generation
Existing Conditions: Borrower has provided Intent to Proceed, and the loan is in the process of generating required disclosures, including HPML checks and Mortgage Broker Fee Agreement (if applicable).

INFO:agents.retrieval_intelligence_agent:🔎 Building retrieval context for WHL
INFO:agents.retrieval_intelligence_agent:🔎 Running vector retrieval for WHL
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index1')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '70705'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '734d68cd-10b9-11f1-86b4-7ced8dc28672'
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
    'request-id': '734d68cd-10b9-11f1-86b4-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Mon, 23 Feb 2026 13:13:31 GMT'
INFO:agents.retrieval_intelligence_agent:📊 Channel WHL → Retrieved 33 documents
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 738152_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 738152_WHL_02
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 742390_WHL_02
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 718523_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 745949_WHL_04
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 718516_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 745949_WHL_03
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 742390_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 745949_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 718524_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 658373_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 749011_WHL_02
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 745949_WHL_02
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 725217_WHL
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 749011_WHL_03
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 734893_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 749011_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 742620_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 738432_WHL_03
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 740128_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 740128_WHL_02
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 737483_WHL_03
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 719229_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 737483_WHL_02
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 737483_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 733851_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 36164_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 738432_WHL_02
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 738432_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 740712_Regression_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → 739659_WHL_01
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → ̘
INFO:agents.retrieval_intelligence_agent:   ↳ WHL → ̘
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:✅ Reranked 1 testcases
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:
📌 Inferred Setup for WHL:
Loan Purpose: Purchase
Loan Type: Conventional
Product: 30-Year Fixed
Loan Stage: Initial Disclosure Generated
Existing Conditions: Broker-originated loan with Intent to Proceed received, Mortgage Broker Fee Agreement signed, and Mortgage Broker License Type validated for California.

INFO:agents.retrieval_intelligence_agent:🔎 Building retrieval context for DTC
INFO:agents.retrieval_intelligence_agent:🔎 Running vector retrieval for DTC
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index1')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '70705'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '76c43a70-10b9-11f1-a406-7ced8dc28672'
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
    'request-id': '76c43a70-10b9-11f1-a406-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Mon, 23 Feb 2026 13:13:36 GMT'
INFO:agents.retrieval_intelligence_agent:📊 Channel DTC → Retrieved 18 documents
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 718524_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 718516_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 738152_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 658373_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 718516_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 734893_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 719229_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 36164_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 718519_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 742620_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 718519_DTC_02
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 719229_DTC_02
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 738432_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 733851_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 740712_Regression_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 738432_DTC_Pipeline_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 739659_DTC_01
INFO:agents.retrieval_intelligence_agent:   ↳ DTC → 745767_DTC_01
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:✅ Reranked 1 testcases
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:
📌 Inferred Setup for DTC:
Loan Purpose: Purchase
Loan Type: Conventional
Product: Fixed Rate 30-Year
Loan Stage: Disclosure Generation
Existing Conditions: Borrower has completed the self-service application and provided initial intent to proceed.

INFO:agents.retrieval_intelligence_agent:🔎 Building retrieval context for CL1
INFO:agents.retrieval_intelligence_agent:🔎 Running vector retrieval for CL1
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index1')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '70705'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '792fd36f-10b9-11f1-b397-7ced8dc28672'
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
    'request-id': '792fd36f-10b9-11f1-b397-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Mon, 23 Feb 2026 13:13:41 GMT'
INFO:agents.retrieval_intelligence_agent:📊 Channel CL1 → Retrieved 21 documents
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 738152_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 742390_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 718516_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 734893_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 745949_CL1_02
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 745949_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 718523_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 658373_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 718524_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 725217_WHL
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 749011_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 740128_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 742620_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 36164_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 737483_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 719229_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 733851_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 738432_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 738432_CL1_02
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → 739659_CL1_01
INFO:agents.retrieval_intelligence_agent:   ↳ CL1 → ̘
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:✅ Reranked 1 testcases
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:
📌 Inferred Setup for CL1:
Loan Purpose: Purchase
Loan Type: Conventional
Product: Fixed Rate 30-Year
Loan Stage: Post-Closing Audit
Existing Conditions: Loan has been purchased from a correspondent lender and is undergoing compliance and disclosure verification.

INFO:agents.retrieval_intelligence_agent:✅ Final Channel Setups Generated
INFO:agents.llm_testcase_generator_agent:🚀 LLM Testcase Generator Agent started
INFO:agents.llm_testcase_generator_agent:🤖 Generating testcase for channel: RTL
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.llm_testcase_generator_agent:🤖 Generating testcase for channel: WHL
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.llm_testcase_generator_agent:🤖 Generating testcase for channel: DTC
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.llm_testcase_generator_agent:🤖 Generating testcase for channel: CL1
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.llm_testcase_generator_agent:✅ LLM generation completed
INFO:agents.excel_export_agent: Excel Export Agent started
INFO:agents.excel_export_agent:Detected channels: ['RTL', 'WHL', 'DTC', 'CL1']
INFO:agents.excel_export_agent:Incoming setup_map keys: ['RTL', 'WHL', 'DTC', 'CL1']
INFO:agents.excel_export_agent:
Formatted precondition for RTL:
Create a loan from Customer Portal as per pre-conditions below:
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: Closing Disclosure Ordered

INFO:agents.excel_export_agent:
Formatted precondition for WHL:
Create a loan from Broker Portal as per pre-conditions below:
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: Created

INFO:agents.excel_export_agent:
Formatted precondition for DTC:
Create a loan from Ignite Portal as per pre-conditions below:
1. Channel: DTC
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: Closing Disclosure Ordered

INFO:agents.excel_export_agent:
Formatted precondition for CL1:
Create a loan from Broker Portal as per pre-conditions below:
1. Channel: CL1
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: Created

INFO:agents.excel_export_agent: Excel generated: output_excels\Indiv_US_718521_Test Scripts_v1.0.xlsx

 Excel Generated at:
output_excels\Indiv_US_718521_Test Scripts_v1.0.xlsx
(.venv) PS C:\Users\h84609n\Desktop\AgenticAI> 
