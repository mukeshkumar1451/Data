 from pydantic.v1.fields import FieldInfo as FieldInfoV1
INFO:agents.ado_intelligence_agent:🚀 ADO Intelligence Agent started
INFO:agents.ado_intelligence_agent:🧹 Processing Description HTML + Images...INFO:agents.ado_intelligence_agent:🧹 Processing Acceptance Criteria HTML + Images...
INFO:utils.channel_detector:Intelligent channel detection started...
INFO:utils.channel_detector: No channel context found → using ALL channels
INFO:agents.ado_intelligence_agent:✅ Channels detected: ['RTL', 'WHL', 'DTC',
 'CL1']

=========== ADO INTELLIGENCE AGENT OUTPUT ===========

User Story ID: 718521
TITLE: Modernized Audit additions - DIS > Generate Disclosures Fields        

CHANNELS: ['RTL', 'WHL', 'DTC', 'CL1']

=====================================================


✅ State dumped to: debug\ado_agent_718521_output.txt

INFO:agents.retrieval_intelligence_agent:🚀 Retrieval Intelligence Agent (Hybrid Mode + Setup Inference)
INFO:agents.retrieval_intelligence_agent:🔎 Hybrid retrieval for RTL
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69089'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'e23c4edf-098c-11f1-8ce7-7ced8dc28672'
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
    'request-id': 'e23c4edf-098c-11f1-8ce7-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:06:52 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69086'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'e2ec3bc0-098c-11f1-b8c8-7ced8dc28672'
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
    'request-id': 'e2ec3bc0-098c-11f1-b8c8-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:06:52 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69091'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'e323d8d7-098c-11f1-ac17-7ced8dc28672'
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
    'request-id': 'e323d8d7-098c-11f1-ac17-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:06:52 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69078'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'e341601f-098c-11f1-8f10-7ced8dc28672'
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
    'request-id': 'e341601f-098c-11f1-8f10-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:06:52 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:Initial retrieval confidence for RTL: 50.00
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:
 Inferred Setup for RTL:
Loan Purpose: Purchase
Loan Type: Conventional
Product: Fixed Rate
Loan Stage: Generate Disclosures
Existing Conditions: HPML, Intent to Proceed, Mortgage Broker Fee/Compensation Agreement, Mortgage Broker License Type (privilege restricted, SubPropState = CA logic applies)

INFO:agents.retrieval_intelligence_agent:🔎 Hybrid retrieval for WHL
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69089'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'e4e23699-098c-11f1-8304-7ced8dc28672'
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
    'request-id': 'e4e23699-098c-11f1-8304-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:06:56 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69111'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'e5464c8f-098c-11f1-a17c-7ced8dc28672'
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
    'request-id': 'e5464c8f-098c-11f1-a17c-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:06:56 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69091'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'e56c10f4-098c-11f1-8a84-7ced8dc28672'
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
    'request-id': 'e56c10f4-098c-11f1-8a84-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:06:56 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69087'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'e58d2ff4-098c-11f1-8ec7-7ced8dc28672'
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
    'request-id': 'e58d2ff4-098c-11f1-8ec7-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:06:57 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:Initial retrieval confidence for WHL: 51.00
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:
 Inferred Setup for WHL:
Loan Purpose: Purchase
Loan Type: Conventional
Product: Fixed Rate
Loan Stage: Approved with Conditions
Existing Conditions: Disclosure-specific conditions exist

INFO:agents.retrieval_intelligence_agent:🔎 Hybrid retrieval for DTC
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69089'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'e7b25931-098c-11f1-b967-7ced8dc28672'
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
    'request-id': 'e7b25931-098c-11f1-b967-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:07:01 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69086'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'e7dc48c3-098c-11f1-a5f2-7ced8dc28672'
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
    'request-id': 'e7dc48c3-098c-11f1-a5f2-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:07:01 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69091'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'e80ecf9a-098c-11f1-afd2-7ced8dc28672'
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
    'request-id': 'e80ecf9a-098c-11f1-afd2-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:07:01 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69114'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'e82f0fc0-098c-11f1-9ef4-7ced8dc28672'
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
    'request-id': 'e82f0fc0-098c-11f1-9ef4-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:07:01 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:Initial retrieval confidence for DTC: 49.00
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:
 Inferred Setup for DTC:
Loan Purpose: Refinance
Loan Type: FHA
Product: Broad Market
Loan Stage: Application Accepted through CD Audit Completed
Existing Conditions: Early Disclosure or Redisclosures Signed Date is populated; SubPropState = CA triggers Mortgage Broker License Type; Privilege restrictions apply to Mortgage Broker Fee/Compensation Agreement and Mortgage Broker License Type.

INFO:agents.retrieval_intelligence_agent:🔎 Hybrid retrieval for CL1
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69089'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'ea3d6168-098c-11f1-8aea-7ced8dc28672'
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
    'request-id': 'ea3d6168-098c-11f1-8aea-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:07:05 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69086'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'ea66ed8a-098c-11f1-af75-7ced8dc28672'
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
    'request-id': 'ea66ed8a-098c-11f1-af75-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:07:05 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69091'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'ea8bb006-098c-11f1-8ef3-7ced8dc28672'
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
    'request-id': 'ea8bb006-098c-11f1-8ef3-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:07:05 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-e2e-knowledge-index')/docs/search.post.search?api-version=REDACTED'   
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '69112'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'eacddc8c-098c-11f1-a2fa-7ced8dc28672'
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
    'request-id': 'eacddc8c-098c-11f1-a2fa-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Sat, 14 Feb 2026 10:07:05 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:Initial retrieval confidence for CL1: 23.00
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:
 Inferred Setup for CL1:
Loan Purpose: Purchase
Loan Type: Conventional
Product: Fixed Rate
Loan Stage: Disclosure Generation
Existing Conditions: SubPropState = CA, Privilege restrictions for Mortgage Broker Fee/Compensation Agreement and Mortgage Broker License Type

INFO:agents.retrieval_intelligence_agent:
 final channel setups: {'RTL': 'Loan Purpose: Purchase  \nLoan Type: Conventional  \nProduct: Fixed Rate  \nLoan Stage: Generate Disclosures  \nExisting Conditions: HPML, Intent to Proceed, Mortgage Broker Fee/Compensation Agreement, Mortgage Broker License Type (privilege restricted, SubPropState = CA logic applies)', 'WHL': 'Loan Purpose: Purchase  \nLoan Type: Conventional  \nProduct: Fixed Rate  \nLoan Stage: Approved with Conditions  \nExisting Conditions: Disclosure-specific conditions exist', 'DTC': 'Loan Purpose: Refinance  \nLoan Type: FHA  \nProduct: Broad Market  \nLoan Stage: Application Accepted 
through CD Audit Completed  \nExisting Conditions: Early Disclosure or Redisclosures Signed Date is populated; SubPropState = CA triggers Mortgage Broker 
License Type; Privilege restrictions apply to Mortgage Broker Fee/Compensation Agreement and Mortgage Broker License Type.', 'CL1': 'Loan Purpose: Purchase  \nLoan Type: Conventional  \nProduct: Fixed Rate  \nLoan Stage: Disclosure Generation  \nExisting Conditions: SubPropState = CA, Privilege restrictions for Mortgage Broker Fee/Compensation Agreement and Mortgage Broker License Type'}

INFO:agents.retrieval_intelligence_agent:
 final retrieval confidence: {'RTL': 50, 'WHL': 51, 'DTC': 49, 'CL1': 23}    

INFO:agents.llm_testcase_generator_agent:🚀 LLM Testcase Generator Agent started
INFO:agents.llm_testcase_generator_agent:
 Precondition for RTL:
Loan Purpose: Purchase
Loan Type: Conventional
Product: Fixed Rate
Loan Stage: Generate Disclosures
Existing Conditions: HPML, Intent to Proceed, Mortgage Broker Fee/Compensation Agreement, Mortgage Broker License Type (privilege restricted, SubPropState = CA logic applies)

INFO:agents.llm_testcase_generator_agent:🤖 Generating testcase for channel: 
RTL
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.llm_testcase_generator_agent:✅ LLM output received for RTL
INFO:agents.llm_testcase_generator_agent:
 Precondition for WHL:
Loan Purpose: Purchase
Loan Type: Conventional
Product: Fixed Rate
Loan Stage: Approved with Conditions
Existing Conditions: Disclosure-specific conditions exist

INFO:agents.llm_testcase_generator_agent:🤖 Generating testcase for channel: 
WHL
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.llm_testcase_generator_agent:✅ LLM output received for WHL
INFO:agents.llm_testcase_generator_agent:
 Precondition for DTC:
Loan Purpose: Refinance
Loan Type: FHA
Product: Broad Market
Loan Stage: Application Accepted through CD Audit Completed
Existing Conditions: Early Disclosure or Redisclosures Signed Date is populated; SubPropState = CA triggers Mortgage Broker License Type; Privilege restrictions apply to Mortgage Broker Fee/Compensation Agreement and Mortgage Broker License Type.

INFO:agents.llm_testcase_generator_agent:🤖 Generating testcase for channel: 
DTC
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.llm_testcase_generator_agent:✅ LLM output received for DTC
INFO:agents.llm_testcase_generator_agent:
 Precondition for CL1:
Loan Purpose: Purchase
Loan Type: Conventional
Product: Fixed Rate
Loan Stage: Disclosure Generation
Existing Conditions: SubPropState = CA, Privilege restrictions for Mortgage Broker Fee/Compensation Agreement and Mortgage Broker License Type

INFO:agents.llm_testcase_generator_agent:🤖 Generating testcase for channel: 
CL1
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.llm_testcase_generator_agent:✅ LLM output received for CL1
INFO:agents.llm_testcase_generator_agent:✅ LLM generation completed for all c
hannels
INFO:agents.excel_export_agent:📄 Excel Export Agent started
INFO:agents.excel_export_agent:
Formatted precondition for RTL:
Create a loan from Customer Portal as per pre-conditions below:
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: Generate Disclosures

INFO:agents.excel_export_agent:
Formatted precondition for WHL:
Create a loan from Broker Portal as per pre-conditions below:
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: Approved with Conditions

INFO:agents.excel_export_agent:
Formatted precondition for DTC:
Create a loan from Ignite Portal as per pre-conditions below:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: FHA
4. Product Code: FF30
5. Loan Stage: Application Accepted through CD Audit Completed

INFO:agents.excel_export_agent:
Formatted precondition for CL1:
Create a loan from Broker Portal as per pre-conditions below:
1. Channel: CL1
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: Disclosure Generation

INFO:agents.excel_export_agent:✅ Excel generated: output_excels\Indiv_US_7185
21_Test Scripts_v1.0.xlsx

✅ Excel Generated at:
output_excels\Indiv_US_718521_Test Scripts_v1.0.xlsx
(.venv) PS C:\Users\h84609n\Desktop\AgenticAI> 
