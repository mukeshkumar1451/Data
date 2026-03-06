INFO:agents.review_agent:Review Agent running
WARNING:agents.review_agent:WHL missing keywords: ['additions']
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.review_agent:WHL → Added Steps:
INFO:agents.review_agent:Step 14 | Verify the additions of new fields in the "Generate Disclosures" section | Generate Disclosures | NA | The system displays the 
newly added fields in the "Generate Disclosures" section | 718521_AC_08
INFO:agents.review_agent:Step 15 | Log out from H2O-A | Application Header | NA | The system terminates the session and redirects to the login page | NA
INFO:agents.review_agent:Saved testcase log: logs/718521_WHL_testcase.txt        
WARNING:agents.review_agent:RTL missing keywords: ['fields', 'additions']        
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.review_agent:RTL → Added Steps:
INFO:agents.review_agent:Step 12 | Verify the presence of the new additions in the Generate Disclosures section | Generate Disclosures | NA | The system displays 
the newly added fields under the Generate Disclosures section | 718521_AC_07     
INFO:agents.review_agent:Step 13 | Validate the functionality of the new additions in the Generate Disclosures section | Generate Disclosures | NA | The system processes the new additions correctly and adheres to the business rules | 718521_AC_07
INFO:agents.review_agent:Step 14 | Click the "Save" button to save the changes | 
Generate Disclosures | NA | The system saves the changes and displays a confirmation message | 718521_AC_06
INFO:agents.review_agent:Step 15 | Log out from H2O-A | Application Header | NA | The system terminates the session and redirects to the login page | NA
INFO:agents.review_agent:Saved testcase log: logs/718521_RTL_testcase.txt        
WARNING:agents.review_agent:DTC missing keywords: ['fields', 'additions']        
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.review_agent:DTC → Added Steps:
INFO:agents.review_agent:Step 23 | Verify the presence of the "Newly Added Fields" section | Generate Disclosures | NA | The system displays the "Newly Added Fields" section | 718521_AC_11
INFO:agents.review_agent:Step 24 | Validate the additions in the "Newly Added Fields" section | Generate Disclosures | NA | The system correctly displays and processes the additions in the "Newly Added Fields" section | 718521_AC_11
INFO:agents.review_agent:Step 25 | Log out from H2O-A | Application Header | NA | The system terminates the session and redirects to the login page | NA
INFO:agents.review_agent:Saved testcase log: logs/718521_DTC_testcase.txt        
WARNING:agents.review_agent:CL1 missing keywords: ['additions']
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.review_agent:CL1 → Added Steps:
INFO:agents.review_agent:Step 18 | Verify the presence of additions in the "Generate Disclosures" section | Generate Disclosures | NA | The system displays the additions in the "Generate Disclosures" section | 718521_AC_09
INFO:agents.review_agent:Step 19 | Log out from H2O-A | Application Header | NA | The system terminates the session and redirects to the login page | NA
INFO:agents.review_agent:Saved testcase log: logs/718521_CL1_testcase.txt        
INFO:agents.excel_export_agent:Excel Export Agent started
INFO:agents.excel_export_agent:Sheets after cleanup: ['RTL', 'DTC', 'WHL', 'CL1']
 Excel Generated at:
output_excels\Indiv_US_718521_Test_Scripts_v1.0.xlsx
