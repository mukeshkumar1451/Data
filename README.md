INFO:agents.llm_testcase_generator_agent:Generating testcases for channel → CL1  
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.llm_testcase_generator_agent:LLM Generation Completed
INFO:agents.review_agent:Review Agent running
WARNING:agents.review_agent:WHL missing keywords: ['generate disclosures fields', 'modernized audit additions']
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.review_agent:Saved testcase log: logs/718521_WHL_testcase.txt
WARNING:agents.review_agent:RTL missing keywords: ['generate disclosures fields', 'modernized audit additions']
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.review_agent:Saved testcase log: logs/718521_RTL_testcase.txt
WARNING:agents.review_agent:DTC missing keywords: ['generate disclosures fields', 'modernized audit additions']
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.review_agent:Saved testcase log: logs/718521_DTC_testcase.txt
WARNING:agents.review_agent:CL1 missing keywords: ['generate disclosures fields', 'modernized audit additions']
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.review_agent:Saved testcase log: logs/718521_CL1_testcase.txt
INFO:agents.excel_export_agent:Excel Export Agent started
INFO:agents.excel_export_agent:Sheets after cleanup: ['RTL', 'DTC', 'WHL', 'CL1']ERROR:agents.excel_export_agent:No valid steps parsed for channel WHL

 Excel Generated at:
output_excels\Indiv_US_718521_Test_Scripts_v1.0.xlsx
(.venv) PS C:\Users\h84609n\Desktop\AgenticAI> 
