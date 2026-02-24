(.venv) PS C:\Users\h84609n\Desktop\AgenticAI> py run_agent.py
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
INFO:agents.retrieval_intelligence_agent:🚀 Retrieval Agent Running
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '73929'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'fdd44628-1181-11f1-9fbb-7ced8dc28672'
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
    'request-id': 'fdd44628-1181-11f1-9fbb-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 13:09:04 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '73929'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'ff4e21d6-1181-11f1-b2b8-7ced8dc28672'
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
    'request-id': 'ff4e21d6-1181-11f1-b2b8-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 13:09:05 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '73929'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '00a47598-1182-11f1-9f37-7ced8dc28672'
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
    'request-id': '00a47598-1182-11f1-9f37-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 13:09:07 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '73929'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '01f5473e-1182-11f1-add9-7ced8dc28672'
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
    'request-id': '01f5473e-1182-11f1-add9-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 13:09:10 GMT'
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:✅ Retrieval Completed
INFO:agents.llm_testcase_generator_agent:🤖 LLM Generator Running
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

### Test Case: Verify Modernized Audit Additions - DIS > Generate Disclosures Fields

---

#### **Test Case ID**: TC_001_MODERNIZED_AUDIT_DIS_FIELDS
#### **Test Case Title**: Verify the addition of new fields in Modernized Audit under DIS > Generate Disclosures     
#### **Test Type**: Functional
#### **Priority**: High
#### **Test Environment**: UAT1
#### **Preconditions**:
1. Admin > Organization Admin > Business Unit user should mark "Is Test Business Unit" = true for the Business Unit: 

   - Test Division (RET-TEST-REG).
2. A loan should be created through the Customer Portal with the following details:
   - **Channel**: RTL
   - **Loan Purpose**: Purchase
   - **Loan Type**: Conventional
   - **Product Code**: CF30
   - **Loan Stage**: Application Accepted

---

### **Test Steps**

#### **Step 1**: Login to H2O-A in UAT1 Environment
- **Description**: Navigate to the H2O-A application and log in using valid credentials.
- **Screen**: Login Page
- **Test Data**:
  - URL: https://uath2o.newrez.com/
  - Valid credentials (username and password).
- **Expected Result**: Login should be successful, and the user should be redirected to the home page.

---

#### **Step 2**: Open the loan created as per the preconditions
- **Description**: Search for the loan created in the preconditions and open it.
- **Screen**: Loan Summary
- **Test Data**: Loan ID or borrower details.
- **Expected Result**: The "Loan Summary" screen should be displayed with the loan details.

---

#### **Step 3**: Verify the "Test Loan Type" field in 1003 > Loan Summary > Company Details
- **Description**: Navigate to 1003 > Loan Summary > Company Details and verify the "Test Loan Type" field.
- **Screen**: Loan Summary
- **Test Data**: N/A
- **Expected Result**: The "Test Loan Type" field should remain unset (Select).

---

#### **Step 4**: Update the Branch Name under the Business Unit
- **Description**: Update the Branch Name to one of the following:
  - Retail Test Branch (RET-Branch General)
  - Retail NRZ Test Branch (RET-NRZ-Branch General)
  - H2O D RTL Test Branch (RET-TEST-H2OD)
- **Screen**: Loan Summary
- **Test Data**: Branch Name
- **Expected Result**: The branch should be successfully updated.

---

#### **Step 5**: Verify the "Test Loan Type" field after updating the branch
- **Description**: Navigate to 1003 > Loan Summary > Company Details and verify the "Test Loan Type" field again.    
- **Screen**: 1003
- **Test Data**: N/A
- **Expected Result**: The "Test Loan Type" field should automatically be set to "Temporary Test Loan".

---

#### **Step 6**: Navigate to the Audit Tool
- **Description**: Navigate to the Tool > Audit section.
- **Screen**: Audit
- **Test Data**: N/A
- **Expected Result**: The Audit screen should load successfully.

---

#### **Step 7**: Verify the addition of new fields in DIS > Generate Disclosures
- **Description**: Navigate to DIS > Generate Disclosures and verify the presence of the following fields:
  - **HPML**
  - **Intent to Proceed**
  - **Mortgage Broker Fee Agreement**
  - **Mortgage Broker License Type**
- **Screen**: DIS > Generate Disclosures
- **Test Data**: N/A
- **Expected Result**:
  - The fields should be displayed as per the requirements.
  - **Mortgage Broker License Type** should only appear when `SubPropState = CA`.
  - Privilege-restricted fields should only be accessible to authorized users.

---

#### **Step 8**: Verify the functionality of the "Intent to Proceed" field
- **Description**: Test the functionality of the "Intent to Proceed" field by selecting different options and verifying the behavior.
- **Screen**: DIS > Generate Disclosures
- **Test Data**: Select options like "Yes" or "No".
- **Expected Result**:
  - The field should allow the user to select an option.
  - The selection should be saved successfully.

---

#### **Step 9**: Verify the functionality of the "Mortgage Broker Fee Agreement" field
- **Description**: Test the functionality of the "Mortgage Broker Fee Agreement" field by selecting "Yes" or "No" and verifying the behavior.
- **Screen**: DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
- **Test Data**: Select options like "Yes" or "No".
- **Expected Result**:
  - The field should allow the user to select an option.
  - The selection should be saved successfully.

---

#### **Step 10**: Verify privilege restrictions for restricted fields
- **Description**: Attempt to access privilege-restricted fields with a user who does not have the required permissions.
- **Screen**: DIS > Generate Disclosures
- **Test Data**: User without required privileges.
- **Expected Result**:
  - The restricted fields should not be accessible to unauthorized users.
  - An appropriate error message should be displayed if access is attempted.

---

### **Postconditions**
1. Ensure that all changes made during the test (e.g., branch updates) are reverted to their original state.
2. Log out of the H2O-A application.

---

### **Test Data Requirements**
1. Loan details as per the preconditions.
2. User credentials with and without privilege access.

---

### **Expected Results Summary**
1. All new fields should be displayed in the specified locations.
2. Fields should function as expected, including privilege restrictions and conditional display logic.
3. All updates and selections should be saved successfully.

---

### **Test Execution Notes**
- Ensure that the test is executed in the UAT1 environment.
- Validate the conditional logic for the "Mortgage Broker License Type" field based on `SubPropState = CA`.
- Verify privilege restrictions thoroughly.

---

This structured test case ensures comprehensive coverage of the new functionality while adhering to the preconditions and acceptance criteria.
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

### Test Case: Verify Modernized Audit Additions - DIS > Generate Disclosures Fields

#### **Test Case ID**: TC_001_MODERNIZED_AUDIT_DIS_FIELDS
#### **Test Objective**: Verify the addition of new fields in the Modernized Audit under DIS > Generate Disclosures. 

#### **Preconditions**:
1. Create a loan with the following preconditions:
   - **Channel**: WHL
   - **Loan Purpose**: Refinance
   - **Loan Type**: FHA
   - **Product**: Any
   - **Loan Stage**: UW Submitted
   - Early Disclosure should be generated, sent via eSign, eSign should be completed, and disclosure should be received.
2. User must have appropriate privileges to access the fields (e.g., Mortgage Broker Fee/Compensation Agreement).    
3. SubPropState must be set to "CA" to verify the "Mortgage Broker License Type" field.

---

### **Test Steps**

#### **Step 1**: Log in to H2O-A in UAT Environment
- **Description**: Navigate to the UAT environment and log in using valid credentials.
- **Screen**: Login Page
- **Test Data**: URL: `https://uath2o.newrez.com`
- **Expected Result**: Login should be successful, and the user should land on the dashboard.

---

#### **Step 2**: Open the created loan
- **Description**: Search and open the loan that meets the preconditions.
- **Screen**: Loan Summary
- **Test Data**: Loan ID or other identifying information.
- **Expected Result**: Loan Summary screen should display the loan details.

---

#### **Step 3**: Navigate to DIS > Generate Disclosures
- **Description**: Navigate to the "Generate Disclosures" section under the DIS module.
- **Screen**: DIS > Generate Disclosures
- **Test Data**: N/A
- **Expected Result**: The "Generate Disclosures" screen should load successfully.

---

#### **Step 4**: Verify the "Intent to Proceed" field
- **Description**: Check if the "Intent to Proceed" field is displayed and editable.
- **Screen**: DIS > Generate Disclosures
- **Test Data**: N/A
- **Expected Result**: The "Intent to Proceed" field should be visible and editable.

---

#### **Step 5**: Verify the "Mortgage Broker Fee/Compensation Agreement" field
- **Description**: Check if the "Mortgage Broker Fee/Compensation Agreement" field is displayed. Verify that it is privilege-restricted.
- **Screen**: DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
- **Test Data**: N/A
- **Expected Result**: The "Mortgage Broker Fee/Compensation Agreement" field should be visible only if the user has 
the required privileges.

---

#### **Step 6**: Verify the "Mortgage Broker License Type" field
- **Description**: Check if the "Mortgage Broker License Type" field is displayed when `SubPropState = CA`. Verify that it is privilege-restricted.
- **Screen**: DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
- **Test Data**: SubPropState = CA
- **Expected Result**: The "Mortgage Broker License Type" field should be visible only if the SubPropState is CA and 
the user has the required privileges.

---

#### **Step 7**: Verify field values in Modernized Audit
- **Description**: Navigate to Tools > Modernized Audit and verify the entries created for the following fields:     
  - Intent to Proceed
  - Mortgage Broker Fee/Compensation Agreement
  - Mortgage Broker License Type
- **Screen**: Tools > Modernized Audit
- **Test Data**: N/A
- **Expected Result**:
  - Entries should be created for the above fields.
  - Each entry should display:
    - User who made the change
    - Time Occurred
    - Previous Value
    - New Value
    - Field Name

---

#### **Step 8**: Verify privilege restrictions
- **Description**: Attempt to access privilege-restricted fields (e.g., Mortgage Broker Fee/Compensation Agreement) with a user who does not have the required privileges.
- **Screen**: DIS > Generate Disclosures
- **Test Data**: User without required privileges.
- **Expected Result**: The restricted fields should not be visible or accessible.

---

#### **Step 9**: Verify field behavior when SubPropState is not CA
- **Description**: Change the SubPropState to a value other than CA and verify the behavior of the "Mortgage Broker License Type" field.
- **Screen**: DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
- **Test Data**: SubPropState = NY (or any other state except CA).
- **Expected Result**: The "Mortgage Broker License Type" field should not be displayed.

---

### **Postconditions**:
1. Ensure all changes made during the test are reverted (e.g., reset SubPropState).
2. Log out of the H2O-A system.

---

### **Test Data**:
- Loan ID: [Dynamic based on preconditions]
- SubPropState: CA (for Step 6), NY (for Step 9)
- User Privileges: Admin (for privileged access), Non-Admin (for restricted access)

---

### **Expected Results Summary**:
1. All new fields should be displayed as per the acceptance criteria.
2. Privilege-restricted fields should only be accessible to authorized users.
3. Field behavior should align with the SubPropState logic.
4. Modernized Audit should capture all changes with accurate details.


INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

### Test Case: Verify Modernized Audit Additions - DIS > Generate Disclosures Fields

---

#### **Test Case ID**: TC_001_MODERNIZED_AUDIT_FIELDS
#### **Test Case Title**: Verify the addition of new fields in Modernized Audit under DIS > Generate Disclosures     
#### **Test Type**: Functional
#### **Priority**: High
#### **Test Environment**: UAT
#### **Preconditions**:
1. A loan is created in the Ignite Portal with the following details:
   - **Channel**: DTC
   - **Loan Purpose**: Refinance
   - **Loan Type**: VA
   - **Product Code**: Buydown product
   - **Stage**: "Application Accepted"
2. User has valid credentials to log in to the H2O-A application.
3. User has appropriate privileges to access the "Modernized Audit" and "Generate Disclosures" sections.

---

### **Test Steps**

#### **Step 1: Login to H2O-A Application**
- **Description**: Log in to the H2O-A application in the UAT environment.
- **Screen**: Login Page
- **Test Data**:
  - URL: `https://uath2o.newrez.com`
  - Valid user credentials
- **Expected Result**:
  - Login is successful, and the user is redirected to the dashboard.

---

#### **Step 2: Open the Created Loan**
- **Description**: Search and open the loan created in the Ignite Portal that meets the preconditions.
- **Screen**: Loan Summary
- **Test Data**: Loan ID or borrower details
- **Expected Result**:
  - Loan Summary screen is displayed for the selected loan.

---

#### **Step 3: Verify Loan Details**
- **Description**: Navigate to the Loan Details section and verify the following fields:
  - Loan Purpose
  - Loan Product Code
  - Buydown
- **Screen**: Loan Summary
- **Test Data**: None
- **Expected Result**:
  - Loan Purpose: Refinance
  - Loan Product Code: Any buydown product code
  - Buydown: Checkbox is checked

---

#### **Step 4: Verify Company Details**
- **Description**: Navigate to the Company Details section and verify the following fields:
  - Loan Originator (Rep)
  - Branch Name (Retail/DTC)
- **Screen**: Loan Summary
- **Test Data**: None
- **Expected Result**:
  - Loan Originator (Rep): Displays the correct loan originator
  - Branch Name: Displays the correct branch (Retail/DTC)

---

#### **Step 5: Navigate to Modernized Audit**
- **Description**: Navigate to **Tools > Modernized Audit** and verify the following fields:
  - LoanPurpose
  - ProductDescription
  - IsBuydown
  - OriginatingBranch
  - OriginatingRep
- **Screen**: Modernized Audit
- **Test Data**: None
- **Expected Result**:
  - LoanPurpose: Displays "Refinance"
  - ProductDescription: Displays the correct product description
  - IsBuydown: Displays "Yes"
  - OriginatingBranch: Displays the correct branch name
  - OriginatingRep: Displays the correct loan originator

---

#### **Step 6: Verify New Fields in Generate Disclosures**
- **Description**: Navigate to **DIS > Generate Disclosures** and verify the presence and functionality of the following fields:
  - **Higher Priced Mortgage Loan (HPML)**
  - **Intent to Proceed**
  - **Mortgage Broker Fee/Compensation Agreement**
  - **Mortgage Broker License Type**
- **Screen**: Generate Disclosures
- **Test Data**:
  - SubPropState = CA (for Mortgage Broker License Type)
- **Expected Result**:
  - **HPML**: Dropdown is present and functional.
  - **Intent to Proceed**: Checkbox is present and functional.
  - **Mortgage Broker Fee/Compensation Agreement**: Option is privilege-restricted and functional.
  - **Mortgage Broker License Type**: Displays when SubPropState = CA and is privilege-restricted.

---

#### **Step 7: Validate Privilege Restrictions**
- **Description**: Verify that the fields **Mortgage Broker Fee/Compensation Agreement** and **Mortgage Broker License Type** are accessible only to users with the appropriate privileges.
- **Screen**: Generate Disclosures
- **Test Data**: User roles with and without privileges
- **Expected Result**:
  - Users with privileges can access and interact with the fields.
  - Users without privileges cannot access the fields.

---

#### **Step 8: Verify Disclosure Generation**
- **Description**: Generate disclosures and validate that the selected fields are included in the generated package. 

- **Screen**: Generate Disclosures
- **Test Data**:
  - Select "Yes" for **Mortgage Broker Fee/Compensation Agreement**
  - SubPropState = CA
- **Expected Result**:
  - Disclosures are successfully generated.
  - Selected fields are included in the disclosure package.

---

### **Postconditions**
1. Disclosures are generated successfully and include the selected fields.
2. Privilege-restricted fields are accessible only to authorized users.

---

### **Test Data**
| Field                          | Value                     |
|--------------------------------|---------------------------|
| Channel                        | DTC                       |
| Loan Purpose                   | Refinance                 |
| Loan Type                      | VA                        |
| Product Code                   | Buydown product           |
| SubPropState                   | CA                        |

---

### **Expected Results Summary**
1. All fields in the Modernized Audit and Generate Disclosures sections are displayed as per the acceptance criteria.
2. Privilege-restricted fields are accessible only to authorized users.
3. Disclosures are generated successfully with the selected fields included.

---

This test case ensures comprehensive validation of the new fields added to the Modernized Audit and Generate Disclosures sections.
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

### Test Case: Verify Modernized Audit Additions - DIS > Generate Disclosures Fields

---

#### **Test Case ID**: TC_001_MODERNIZED_AUDIT_DIS_FIELDS
#### **Test Title**: Verify the addition of new fields in Modernized Audit under DIS > Generate Disclosures
#### **Test Objective**: Ensure that the newly added fields (HPML, Intent to Proceed, Mortgage Broker Fee Agreement, 
and Mortgage Broker License Type) are displayed and function as expected in the Modernized Audit system.

---

### **Preconditions**:
1. Admin > Organization Admin > Business Unit user should mark "Is Test Business Unit" = true for the Business Unit: 
**Test Region (CL-TEST-REG)**.
2. User should have appropriate privileges to access the **Generate Disclosures** section and view restricted fields.3. Ensure the test environment is set to **UAT1**: `https://uath2o.newrez.com/`.
4. SubPropState should be set to **CA** to verify the logic for the Mortgage Broker License Type field.
5. Test Business Unit and Branch Name should be configured as **CL-TEST-REG-AREA**.

---

### **Test Steps**:

#### **Step 1**: Login to the H2O Application
- **Description**: Navigate to the H2O application and log in using valid credentials.
- **Screen**: Login Page
- **Test Data**:
  - URL: `https://uath2o.newrez.com/`
  - Username: `<valid_username>`
  - Password: `<valid_password>`
- **Expected Result**: User should be successfully logged in and redirected to the dashboard.

---

#### **Step 2**: Create a New Loan
- **Description**: Navigate to the "Create New Loan" section and initiate a new loan creation process.
- **Screen**: Create New Loan
- **Test Data**:
  - Branch Name: **CL-TEST-REG-AREA**
  - Loan Creation Method: **Create Loan with Servicing Data**
- **Expected Result**: The "Create New Loan" screen should open, and the loan creation process should be initiated.  

---

#### **Step 3**: Verify the Test Loan Type Field
- **Description**: Ensure that the "Test Loan Type" field is automatically set to "Temporary Test Loan".
- **Screen**: Create New Loan
- **Test Data**: None
- **Expected Result**: The "Test Loan Type" field should display **Temporary Test Loan** by default.

---

#### **Step 4**: Fill Mandatory Fields and Create Loan
- **Description**: Fill in all mandatory fields required for loan creation and proceed to create the loan.
- **Screen**: Create New Loan
- **Test Data**: Provide valid data for mandatory fields (e.g., Borrower Name, Loan Amount, etc.).
- **Expected Result**: A confirmation popup should display: "Your Loan has been Created. Your new loan number is XXXXXXXXX."

---

#### **Step 5**: Navigate to Generate Disclosures Section
- **Description**: Navigate to **DIS > Generate Disclosures** for the newly created loan.
- **Screen**: Loan Details > Generate Disclosures
- **Test Data**: Loan Number: `<newly_created_loan_number>`
- **Expected Result**: The "Generate Disclosures" screen should open successfully.

---

#### **Step 6**: Verify the Presence of New Fields
- **Description**: Verify that the following fields are displayed in the "Generate Disclosures" section:
  - **HPML**
  - **Intent to Proceed**
  - **Mortgage Broker Fee Agreement**
  - **Mortgage Broker License Type** (visible only when SubPropState = CA).
- **Screen**: Generate Disclosures
- **Test Data**:
  - SubPropState: **CA** (for Mortgage Broker License Type).
- **Expected Result**:
  - **HPML** field should be displayed and functional.
  - **Intent to Proceed** field should be displayed and functional.
  - **Mortgage Broker Fee Agreement** field should be displayed and functional.
  - **Mortgage Broker License Type** field should be displayed only when SubPropState = CA.

---

#### **Step 7**: Verify Privilege Restrictions
- **Description**: Verify that the fields **Mortgage Broker Fee Agreement** and **Mortgage Broker License Type** are 
privilege-restricted and accessible only to authorized users.
- **Screen**: Generate Disclosures
- **Test Data**: User Role: `<privileged_user>`
- **Expected Result**:
  - Privileged users should be able to view and interact with the restricted fields.
  - Non-privileged users should not see these fields.

---

#### **Step 8**: Validate Field Functionality
- **Description**: Test the functionality of each field by entering/selecting valid data and triggering any associated actions.
- **Screen**: Generate Disclosures
- **Test Data**:
  - HPML: Select a valid option.
  - Intent to Proceed: Select "Yes" or "No".
  - Mortgage Broker Fee Agreement: Include/Exclude the agreement.
  - Mortgage Broker License Type: Select a valid license type (e.g., DRE, RML).
- **Expected Result**:
  - Data should be saved successfully for each field.
  - Any associated actions (e.g., validations, popups) should trigger as expected.

---

#### **Step 9**: Verify Data Persistence
- **Description**: Save the changes and reopen the loan to verify that the entered data persists.
- **Screen**: Loan Details > Generate Disclosures
- **Test Data**: Loan Number: `<newly_created_loan_number>`
- **Expected Result**: The data entered in the new fields should persist and display correctly upon reopening.       

---

### **Postconditions**:
1. Ensure all changes made during the test are reverted (if applicable).
2. Log out of the H2O application.

---

### **Test Data**:
| Field                          | Test Data Example          |
|--------------------------------|----------------------------|
| Branch Name                    | CL-TEST-REG-AREA           |
| SubPropState                   | CA                         |
| Loan Creation Method           | Create Loan with Servicing Data |
| HPML                           | Yes/No                     |
| Intent to Proceed              | Yes/No                     |
| Mortgage Broker Fee Agreement  | Include/Exclude            |
| Mortgage Broker License Type   | DRE/RML                    |

---

### **Expected Results Summary**:
1. All new fields should be displayed and functional as per the acceptance criteria.
2. Privilege-restricted fields should be accessible only to authorized users.
3. Data entered in the fields should persist and trigger any associated actions or validations.

---

### **Test Status**: Pending Execution
INFO:agents.llm_testcase_generator_agent:✅ LLM Generation Completed
INFO:agents.excel_export_agent:Excel Export Agent started
INFO:agents.excel_export_agent:Detected channels: ['RTL', 'WHL', 'DTC', 'CL1']
INFO:agents.excel_export_agent:Incoming setup_map keys: []
WARNING:agents.excel_export_agent:No setup found for RTL
INFO:agents.excel_export_agent:
Formatted precondition for RTL:
Channel: RTL

WARNING:agents.excel_export_agent:No setup found for WHL
INFO:agents.excel_export_agent:
Formatted precondition for WHL:
Channel: WHL

WARNING:agents.excel_export_agent:No setup found for DTC
INFO:agents.excel_export_agent:
Formatted precondition for DTC:
Channel: DTC

WARNING:agents.excel_export_agent:No setup found for CL1
INFO:agents.excel_export_agent:
Formatted precondition for CL1:
Channel: CL1

INFO:agents.excel_export_agent:Excel generated: output_excels\Indiv_US_718521_Test Scripts_v1.0.xlsx

 Excel Generated at:
output_excels\Indiv_US_718521_Test Scripts_v1.0.xlsx
(.venv) PS C:\Users\h84609n\Desktop\AgenticAI>


