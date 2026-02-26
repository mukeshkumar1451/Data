ydantic V1 functionality isn't compatible with Python 3.14 or greater.
  from pydantic.v1.fields import FieldInfo as FieldInfoV1
INFO:agents.llm_testcase_generator_agent:✅ LLM Testcase Generator initialized
INFO:agents.ado_intelligence_agent:🚀 ADO Intelligence Agent started
OCR DEBUG: Extracted text length = 301
OCR DEBUG: Extracted text length = 44
OCR DEBUG: Extracted text length = 614
OCR DEBUG: Extracted text length = 543
OCR DEBUG: Extracted text length = 301
OCR DEBUG: Extracted text length = 44
OCR DEBUG: Extracted text length = 614
OCR DEBUG: Extracted text length = 543
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openais?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openais?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:utils.channel_detector:Behavioral channel detection started...
INFO:utils.channel_detector:No strong signal → using ALL channels

=========== ADO INTELLIGENCE AGENT OUTPUT ===========

TITLE: Modernized Audit additions - DIS > Generate Disclosures Fields

--- DESCRIPTION STEPS ---

Here is a structured flow of user actions, system responses, and conditions based 

---

### **Flow for Adding Fields to Modernized Audit**

#### **1. User Action: Navigate to Generate Disclosures**
- **System Response:** Display the following options:
  - Generate Disclosure
  - Intent to Proceed
  - Mortgage Broker Fee/Compensation Agreement
  - Mortgage Broker License Type

---

#### **2. User Action: Select "Generate Disclosure"**
- **System Response:** Display options for:
  - Higher Priced Mortgage Loan (HPML)
  - Intent to Proceed
  - Mortgage Broker Fee/Compensation Agreement
  - Mortgage Broker License Type

---

#### **3. User Action: Select "Intent to Proceed"**
- **System Response:** Allow the user to:
  - Enable appraisal order
  - Perform compliance checks (e.g., Ceypass compliance check)
  - Ignore 3rd Party Fee Check
  - Verify title fees after Loan Amount (LA) increase
  - Ignore Fee Quote Data validations

---

#### **4. User Action: Select "Mortgage Broker Fee/Compensation Agreement"**
- **System Response:** Display the following:
  - Prompt: "Do you want to include Mortgage Broker Fee/Compensation Agreement in 
    - Options: Yes / No
  - If "Yes" is selected:
    - **Condition:** Check if the form is suitable for the transaction or broker.
    - **System Response:** Append the Mortgage Broker Fee/Compensation Agreement t
  - If "No" is selected:
    - **System Response:** Do not append the form.

---

#### **5. User Action: Select "Mortgage Broker License Type"**
- **System Response:** Display the following:
  - Prompt: "Under which license will you originate this loan?"
    - Options: Select from available licenses (e.g., DRE, RML).
  - **Condition:** If `SubPropState = CA`, display the Mortgage Broker License Typ
  - **Condition:** Check for privilege restrictions before displaying the section.

---

### **Additional Notes**
- **Privilege Restrictions:**
  - The "Mortgage Broker Fee/Compensation Agreement" and "Mortgage Broker License ge-restricted.
  - Ensure the user has the necessary permissions to access these sections.
- **Logic for Mortgage Broker License Type:**
  - The exact logic for displaying the Mortgage Broker License Type section is unche logic.
- **Electronic Delivery:**
  - If the user has consented to receive disclosures electronically, disclosures w
  - Otherwise, disclosures will be sent via mail.

---

### **Manage Additional Broker Disclosures**
#### **User Action: Navigate to "Manage Additional Broker Disclosures"**
- **System Response:** Provide the ability to:
  - Append additional disclosures to the Newrez LE Package.
  - Display disclosures to be appended (default: 0).

---

This structured flow ensures clarity in user actions, system responses, and condity.

--- ACCEPTANCE CRITERIA STEPS ---

Here is a structured flow of user actions, system responses, and conditions based 

---

### **Flow for Adding Fields to Modernized Audit**

#### **1. User Action: Navigate to Generate Disclosures**
- **System Response:** Display the following options:
  - Generate Disclosure
  - Intent to Proceed
  - Mortgage Broker Fee/Compensation Agreement
  - Mortgage Broker License Type

---

#### **2. User Action: Select "Generate Disclosure"**
- **System Response:** Display options for generating disclosures:
  - Higher Priced Mortgage Loan: [Select Yes/No]
  - Allow Appraisal Order: [Select Yes/No]
  - Compliance Check: [Select Yes/No]
  - Ignore 3rd Party Fee Check: [Select Yes/No]
  - Title Fees Verified after Loan Amount Increase: [Select Yes/No]
  - Ignore Fee Quote Data Validations: [Select Yes/No]

---

#### **3. User Action: Select "Intent to Proceed"**
- **System Response:** Display the following:
  - Option to send via [eSign/Mail]
  - Confirmation of compliance checks
  - Option to override compliance checks (if applicable)

---

#### **4. User Action: Select "Mortgage Broker Fee/Compensation Agreement"**
- **System Response:** Display the following:
  - Prompt: "Do you want to include Mortgage Broker Fee/Compensation Agreement in /No]
  - If "Yes" is selected:
    - Display: "Under which license will you originate this loan?" [Select License
    - Note: "This form may not be suitable for every transaction or broker. Review
requirements prior to including in the file."
  - If "No" is selected:
    - System does not include the agreement in the package.

---

#### **5. User Action: Select "Mortgage Broker License Type"**
- **Condition:** If `SubPropState = CA`:
  - **System Response:** Display the Mortgage Broker License Type section.
  - Note: This section appears to be privilege-restricted.
- **Condition:** If `SubPropState != CA`:
  - **System Response:** Do not display the Mortgage Broker License Type section.

---

#### **6. User Action: Manage Additional Broker Disclosures**
- **System Response:** Provide the ability to:
  - Append additional disclosures to the Newrez LE Package.
  - Display disclosures to be appended: [List of Disclosures].
  - Confirm if the user has consented to receive disclosures electronically. If nol.

---

### **Additional Notes:**
- Some fields and sections (e.g., Mortgage Broker License Type) are privilege-restmissions are in place.
- Logic for displaying the Mortgage Broker License Type section is dependent on th team to confirm exact logic.
- Ensure compliance checks are performed before allowing users to proceed with gen

---

This structured flow ensures clarity in user actions, system responses, and condity.

=====================================================


 State dumped to: debug\ado_agent_718521_output.txt

INFO:agents.ado_intelligence_agent:✅ ADO Intelligence Agent completed
INFO:agents.retrieval_intelligence_agent:🚀 Retrieval Agent Running
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c0search.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.se
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '75295'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '01c292b6-1301-11f1-96db-7ced8dc28672'
    'User-Agent': 'azsdk-python-search-documents/11.6.0 Python/3.14.2 (Windows-10-
A body is sent with the request
INFO:azure.core.pipeline.policies.http_logging_policy:Response status: 200
Response headers:
    'Transfer-Encoding': 'chunked'
    'Content-Type': 'application/json; odata.metadata=none; odata.streaming=true; 
    'Content-Encoding': 'REDACTED'
    'Vary': 'REDACTED'
    'Strict-Transport-Security': 'REDACTED'
    'Preference-Applied': 'REDACTED'
    'OData-Version': 'REDACTED'
    'request-id': '01c292b6-1301-11f1-96db-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Thu, 26 Feb 2026 10:50:48 GMT'
INFO:agents.retrieval_intelligence_agent:RTL → Retrieved 19 docs
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openais?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:RTL → Selected Precondition:
Create a loan from Customer Portal:
1. Channel: RTL
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product Code: Any
5. Loan stage should be UW Submitted
6. Early Disclosure should be generated, sent via eSign ,Esign should be completed
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c0search.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.se
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '75295'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '0440bc06-1301-11f1-a8a3-7ced8dc28672'
    'User-Agent': 'azsdk-python-search-documents/11.6.0 Python/3.14.2 (Windows-10-
A body is sent with the request
INFO:azure.core.pipeline.policies.http_logging_policy:Response status: 200
Response headers:
    'Transfer-Encoding': 'chunked'
    'Content-Type': 'application/json; odata.metadata=none; odata.streaming=true; 
    'Content-Encoding': 'REDACTED'
    'Vary': 'REDACTED'
    'Strict-Transport-Security': 'REDACTED'
    'Preference-Applied': 'REDACTED'
    'OData-Version': 'REDACTED'
    'request-id': '0440bc06-1301-11f1-a8a3-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Thu, 26 Feb 2026 10:50:51 GMT'
INFO:agents.retrieval_intelligence_agent:WHL → Retrieved 20 docs
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openais?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:WHL → Selected Precondition:
Create a Loan from Broker Portal with the below pre-conditions:
1. Channel: WHL
2. Loan Purpose: Refinance
3. Loan Type: FHA
4. Product: Any
5. Loan stage should be UW Submitted
6. Early Disclosure should be generated, sent via eSign ,Esign should be completed
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c0search.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.se
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '75295'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '05b1171a-1301-11f1-9249-7ced8dc28672'
    'User-Agent': 'azsdk-python-search-documents/11.6.0 Python/3.14.2 (Windows-10-
A body is sent with the request
INFO:azure.core.pipeline.policies.http_logging_policy:Response status: 200
Response headers:
    'Transfer-Encoding': 'chunked'
    'Content-Type': 'application/json; odata.metadata=none; odata.streaming=true; 
    'Content-Encoding': 'REDACTED'
    'Vary': 'REDACTED'
    'Strict-Transport-Security': 'REDACTED'
    'Preference-Applied': 'REDACTED'
    'OData-Version': 'REDACTED'
    'request-id': '05b1171a-1301-11f1-9249-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Thu, 26 Feb 2026 10:50:53 GMT'
INFO:agents.retrieval_intelligence_agent:DTC → Retrieved 18 docs
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openais?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:DTC → Selected Precondition:
Create a new loan from Ignite Portal with the following pre-condition:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product: CF30
5. Loan Stage: Application Accepted

INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c0search.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.se
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '75295'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '06c39e1f-1301-11f1-be5b-7ced8dc28672'
    'User-Agent': 'azsdk-python-search-documents/11.6.0 Python/3.14.2 (Windows-10-
A body is sent with the request
INFO:azure.core.pipeline.policies.http_logging_policy:Response status: 200
Response headers:
    'Transfer-Encoding': 'chunked'
    'Content-Type': 'application/json; odata.metadata=none; odata.streaming=true; 
    'Content-Encoding': 'REDACTED'
    'Vary': 'REDACTED'
    'Strict-Transport-Security': 'REDACTED'
    'Preference-Applied': 'REDACTED'
    'OData-Version': 'REDACTED'
    'request-id': '06c39e1f-1301-11f1-be5b-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Thu, 26 Feb 2026 10:50:55 GMT'
INFO:agents.retrieval_intelligence_agent:CL1 → Retrieved 20 docs
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openais?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:CL1 → Selected Precondition:
Create a loan from BP:
1. Channel: CL1
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: Any
5. Loan stage should be UW Submitted
6. Early Disclosure should be generated, sent via eSign ,Esign should be completed
INFO:agents.retrieval_intelligence_agent:Selected Preconditions Map:
INFO:agents.retrieval_intelligence_agent:{'RTL': 'Create a loan from Customer Portse: Refinance\n3. Loan Type: Conventional\n4. Product Code: Any\n5. Loan stage shoosure should be generated, sent via eSign ,Esign should be completed and disclosurate a Loan from Broker Portal with the below pre-conditions:\n1. Channel: WHL\n2. pe: FHA\n4. Product: Any \n5. Loan stage should be UW Submitted\n6. Early Disclosuign ,Esign should be completed and disclosure should be received.', 'DTC': 'Create the following pre-condition:\n1. Channel: DTC\n2. Loan Purpose: Refinance\n3. LoaF30 \n5. Loan Stage: Application Accepted', 'CL1': 'Create a loan from BP:\n1. Chae\n3. Loan Type: Conventional\n4. Product Code: Any\n5. Loan stage should be UW Su be generated, sent via eSign ,Esign should be completed and disclosure should be 
INFO:agents.retrieval_intelligence_agent:✅ Retrieval Completed
INFO:agents.llm_testcase_generator_agent:🤖 LLM Generator Running
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openais?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openais?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openais?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai> Parsed 0 steps                    TP/1.1 200 OK"
INFO:agents.excel_export_agent:CL1 -> Parsed 0 steps
INFO:agents.excel_export_agent:Excel generated: output_excels\Indiv_US_718521_Test_Scripts_v1.0.xlsx

 Excel Generated at:
output_excels\Indiv_US_718521_Test_Scripts_v1.0.xlsx
