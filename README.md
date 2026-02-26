C:\Users\h84609n\Desktop\AgenticAI\.venv\Lib\site-packages\langchain_core\_api\deprecation.py:25: UserWarning: Core Pydantic V1 functionality isn't compatible with Python 3.14 or greater.
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
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:utils.channel_detector:Behavioral channel detection started...
INFO:utils.channel_detector:No strong signal → using ALL channels

=========== ADO INTELLIGENCE AGENT OUTPUT ===========

TITLE: Modernized Audit additions - DIS > Generate Disclosures Fields

--- DESCRIPTION STEPS ---

### Flow Structured Sentences for QA Automation Testing

#### **User Actions, System Responses, and Conditions**

---

### **Adding Fields to Modernized Audit**

#### **User Action:**
1. Navigate to **H2O UI Location**: `HPMLDIS > Generate Disclosures > Generate Disclosure`.
2. Select the following fields:
   - **Intent to Proceed**
   - **Mortgage Broker Fee Agreement**
   - **Mortgage Broker License Type**

#### **System Response:**
1. **Intent to Proceed** field is displayed under `DIS > Generate Disclosures`.
2. **Mortgage Broker Fee Agreement** field is displayed under `DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement`.
3. **Mortgage Broker License Type** field is displayed under `DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement`.

#### **Conditions:**
1. **Mortgage Broker License Type** appears when `SubPropState = CA`. Logic to be confirmed by development team.     
2. Both **Mortgage Broker Fee Agreement** and **Mortgage Broker License Type** are privilege-restricted fields.      

---

### **Generate Disclosure Workflow**

#### **User Action:**
1. Select **Generate Disclosure** from the menu.
2. Choose **Intent to Proceed**.

#### **System Response:**
1. System allows appraisal order.
2. Compliance checks are performed:
   - **Ceypass Compliance Check** is executed.
   - **Ignore 3rd Party Fee Check** is applied.
   - **Title Fees Verified** after Loan Amount (LA) increase.
3. Fee Quote Data validations are ignored.

#### **Conditions:**
1. Higher Priced Mortgage Loan (HPML) selection is required:
   - Options: `[Select...]`, `Yes`, or `No`.
2. Disclosure delivery method is selected:
   - Options: `Electronic Delivery`, `Mail`.

---

### **Mortgage Broker Fee/Compensation Agreement**

#### **User Action:**
1. Select whether to include **Mortgage Broker Fee/Compensation Agreement** in the Newrez LE Package:
   - Options: `[Yes]` or `[No]`.

#### **System Response:**
1. If **Yes** is selected:
   - System appends the **Mortgage Broker Fee/Compensation Agreement** to the Newrez LE Package.
   - User is prompted to select the license under which the loan will originate:
     - Options: `[DRE]`, `[RML]`, or `[Other]`.
2. If **No** is selected:
   - System does not append the agreement to the package.

#### **Conditions:**
1. User must review the form to ensure it meets license/registration disclosure requirements.
2. This form may not be suitable for every transaction or broker.

---

### **Manage Additional Broker Disclosures**

#### **User Action:**
1. Navigate to **Manage Broker Disclosures**.
2. Select additional disclosures to append to the Newrez LE Package.

#### **System Response:**
1. System displays a list of disclosures available for appending.
2. User can append disclosures to the package.

#### **Conditions:**
1. Disclosures appended must comply with license/registration requirements.

---

### **Electronic Disclosure Consent**

#### **User Action:**
1. Select the disclosure delivery method:
   - Options: `[Electronic Delivery]` or `[Mail]`.

#### **System Response:**
1. If **Electronic Delivery** is selected:
   - System verifies that the user has consented to receive disclosures electronically.
2. If **Mail** is selected:
   - Disclosures are sent via mail.

#### **Conditions:**
1. User consent is required for electronic delivery.

---

### **Summary of Key Fields and Logic**

#### **Fields to be Added:**
1. **Intent to Proceed**
2. **Mortgage Broker Fee Agreement**
3. **Mortgage Broker License Type**

#### **Privilege Restrictions:**
1. **Mortgage Broker Fee Agreement** and **Mortgage Broker License Type** are privilege-restricted.

#### **State-Specific Logic:**
1. **Mortgage Broker License Type** appears when `SubPropState = CA`. Development team to confirm logic.

#### **Compliance Checks:**
1. **Ceypass Compliance Check** is executed.
2. **Ignore 3rd Party Fee Check** is applied.
3. **Title Fees Verified** after Loan Amount increase.

---

This structured flow ensures clarity in identifying user actions, system responses, and conditions for QA automation 
testing.

--- ACCEPTANCE CRITERIA STEPS ---

Here is a structured flow of user actions, system responses, and conditions based on the provided content:

---

### **Flow: Adding Fields to Modernized Audit**

#### **User Action: Navigate to Generate Disclosures**
1. **User Action:** Access the "Generate Disclosures" section in the H2O UI under the HPMLDIS module.
2. **System Response:** Display available disclosure options, including:
   - Intent to Proceed
   - Mortgage Broker Fee/Compensation Agreement
   - Mortgage Broker License Type

---

#### **Field: Intent to Proceed**
1. **User Action:** Select "Intent to Proceed" from the disclosure options.
2. **System Response:** Allow appraisal order functionality.
3. **Condition:** If appraisal order is allowed, proceed with compliance checks:
   - Ignore 3rd Party Fee Check.
   - Verify title fees after Loan Amount (LA) increase.
   - Ignore Fee Quote Data validations.

---

#### **Field: Mortgage Broker Fee/Compensation Agreement**
1. **User Action:** Select "Mortgage Broker Fee/Compensation Agreement" from the disclosure options.
2. **System Response:** Display privilege-restricted access to the form.
3. **Condition:** If user has privileges, allow inclusion of the form in the Newrez LE Package.
   - **User Action:** Confirm inclusion of the form.
   - **System Response:** Append the form to the Newrez LE Package.
4. **Condition:** If user does not have privileges, restrict access to the form.

---

#### **Field: Mortgage Broker License Type**
1. **User Action:** Select "Mortgage Broker License Type" from the disclosure options.
2. **System Response:** Display privilege-restricted access to the section.
3. **Condition:** If SubPropState = CA, display the license section.
   - **System Response:** Dev to confirm logic for displaying this section.
4. **Condition:** If user has privileges, allow access to the section.
   - **User Action:** Select the license type under which the loan will originate.
   - **System Response:** Append the license type to the Newrez LE Package.

---

### **Flow: Generating Disclosures**

#### **User Action: Generate Disclosure**
1. **User Action:** Select "Generate Disclosure" from the available options.
2. **System Response:** Display options for sending disclosures:
   - **Condition:** If user has consented to receive disclosures electronically:
     - Send via eSign.
   - **Condition:** If user has not consented:
     - Send via Mail.

---

### **Flow: Managing Broker Disclosures**

#### **User Action: Manage Broker Disclosures**
1. **User Action:** Access "Manage Broker Disclosures" functionality.
2. **System Response:** Provide options to append additional disclosures to the Newrez LE Package.
3. **Condition:** If user selects "Mortgage Broker Fee/Compensation Agreement":
   - **System Response:** Display a note indicating the form may not be suitable for every transaction or broker.    
   - **User Action:** Review license/registration’s disclosure requirements.
   - **System Response:** Append the form to the Newrez LE Package if requirements are met.

---

### **Additional Notes**
- **Privilege Restrictions:** Certain fields (e.g., Mortgage Broker Fee/Compensation Agreement, Mortgage Broker License Type) are privilege-restricted.
- **Logic Confirmation:** Dev team to confirm logic for displaying the Mortgage Broker License Type section when SubPropState = CA.
- **Compliance Checks:** Ensure compliance checks are performed for appraisal orders and fee validations.

---

This structured flow ensures clarity in user actions, system responses, and conditions for each step in the process. 

=====================================================


 State dumped to: debug\ado_agent_718521_output.txt

INFO:agents.ado_intelligence_agent:✅ ADO Intelligence Agent completed
INFO:agents.retrieval_intelligence_agent:🚀 Retrieval Agent Running
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '77100'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '91a33a58-1303-11f1-9d5f-7ced8dc28672'
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
    'request-id': '91a33a58-1303-11f1-9d5f-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Thu, 26 Feb 2026 11:09:08 GMT'
INFO:agents.retrieval_intelligence_agent:RTL → Retrieved 19 docs
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:RTL → Selected Precondition:
Create a loan from Customer Portal:
1. Channel: RTL
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product Code: Any
5. Loan stage should be UW Submitted
6. Early Disclosure should be generated, sent via eSign ,Esign should be completed and disclosure should be received.
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '77100'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '933db00c-1303-11f1-b561-7ced8dc28672'
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
    'request-id': '933db00c-1303-11f1-b561-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Thu, 26 Feb 2026 11:09:10 GMT'
INFO:agents.retrieval_intelligence_agent:WHL → Retrieved 20 docs
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:WHL → Selected Precondition:
Create a Loan from Broker Portal with the below pre-conditions:
1. Channel: WHL
2. Loan Purpose: Refinance
3. Loan Type: FHA
4. Product: Any
5. Loan stage should be UW Submitted
6. Early Disclosure should be generated, sent via eSign ,Esign should be completed and disclosure should be received.
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '77100'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '941f3fcf-1303-11f1-93cd-7ced8dc28672'
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
    'request-id': '941f3fcf-1303-11f1-93cd-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Thu, 26 Feb 2026 11:09:11 GMT'
INFO:agents.retrieval_intelligence_agent:DTC → Retrieved 18 docs
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:DTC → Selected Precondition:
Create a new loan from Ignite Portal with the following pre-condition:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product: CF30
5. Loan Stage: Application Accepted

INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/text-embedding-3-large/embeddings?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:azure.core.pipeline.policies.http_logging_policy:Request URL: 'https://uat-c01-ai-productivity-transformation-aisearch.search.windows.net/indexes('mortgage-knowledge-index2')/docs/search.post.search?api-version=REDACTED'
Request method: 'POST'
Request headers:
    'Content-Type': 'application/json'
    'Content-Length': '77100'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': '9536acc9-1303-11f1-8077-7ced8dc28672'
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
    'request-id': '9536acc9-1303-11f1-8077-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Thu, 26 Feb 2026 11:09:13 GMT'
INFO:agents.retrieval_intelligence_agent:CL1 → Retrieved 20 docs
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:CL1 → Selected Precondition:
Create a loan from BP:
1. Channel: CL1
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: Any
5. Loan stage should be UW Submitted
6. Early Disclosure should be generated, sent via eSign ,Esign should be completed and disclosure should be received.
INFO:agents.retrieval_intelligence_agent:Selected Preconditions Map:
INFO:agents.retrieval_intelligence_agent:{'RTL': 'Create a loan from Customer Portal:\n1. Channel: RTL\n2. Loan Purpose: Refinance\n3. Loan Type: Conventional\n4. Product Code: Any\n5. Loan stage should be UW Submitted\n6. Early Disclosure should be generated, sent via eSign ,Esign should be completed and disclosure should be received.', 'WHL': 'Create a Loan from Broker Portal with the below pre-conditions:\n1. Channel: WHL\n2. Loan Purpose: Refinance\n3. Loan Type: FHA\n4. Product: Any \n5. Loan stage should be UW Submitted\n6. Early Disclosure should be generated, sent via eSign ,Esign should be completed and disclosure should be received.', 'DTC': 'Create a new loan from Ignite Portal with the following pre-condition:\n1. Channel: DTC\n2. Loan Purpose: Refinance\n3. Loan Type: Conventional\n4. Product: CF30 \n5. Loan Stage: Application Accepted', 'CL1': 'Create a loan from BP:\n1. Channel: CL1\n2. Loan Purpose: Purchase\n3. Loan Type: Conventional\n4. Product Code: Any\n5. Loan stage should be UW Submitted\n6. Early Disclosure should be generated, sent via eSign ,Esign should be completed and disclosure should be received.'}
INFO:agents.retrieval_intelligence_agent:✅ Retrieval Completed
INFO:agents.llm_testcase_generator_agent:🤖 LLM Generator Running
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
LLM Outputs: {'RTL': '### Test Case Generation\n\n---\n\n**Test Case ID / Test Script ID:** 718521_RTL_01  \n**Test Scenario Id:** 718521_SC_01  \n**Test Scenario Description:** Validate business rules for generating disclosures in RTL channel loans.  \n**Test Script Description:** Ensure system behavior aligns with business rules for disclosure generation, privilege restrictions, and compliance checks in RTL channel loans.  \n**Pre-Condition & Assumptions:** Refer to provided precondition context.\n\n---\n\n### Test Steps\n\n| Test Step No. | Test Step Description | Screen Name | Test Data | Expected Results | Requirement Mapping |\n|---------------|-----------------------|-------------|-----------|------------------|----------------------|\n| Step 01 | Navigate to "Generate Disclosures" section in H2O UI under HPMLDIS module. | Generate Disclosures | NA | System displays available disclosure options: Intent to Proceed, Mortgage Broker Fee Agreement, Mortgage Broker License Type. | 718521_AC_01 |\n| Step 02 | Select "Intent to Proceed" 
from the disclosure options. | Generate Disclosures | NA | System allows appraisal order functionality. Compliance checks are performed: Ignore 3rd Party Fee Check, Verify title fees after Loan Amount increase, Ignore Fee Quote Data validations. | 718521_AC_02 |\n| Step 03 | Select "Mortgage Broker Fee Agreement" from the disclosure options. | Generate Disclosures | NA | System restricts access to the form for users without privileges. If user has privileges, form is appended to the Newrez LE Package. | 718521_AC_03 |\n| Step 04 | Select "Mortgage Broker License Type" from the disclosure options. | Generate Disclosures | NA | System restricts access to the section for users without privileges. If SubPropState = CA, system displays the license section. | 718521_AC_04 |\n| Step 05 | Confirm inclusion of "Mortgage Broker Fee Agreement" in the Newrez LE Package. | Generate Disclosures | NA | System appends the form to the Newrez LE Package if user has privileges. | 718521_AC_05 |\n| Step 06 | Select the license type under which the loan will originate (e.g., DRE, RML, Other). | Generate Disclosures | NA | System appends the selected license type to the Newrez LE Package. | 718521_AC_06 |\n| Step 07 | Select "Generate Disclosure" from the available options. | Generate Disclosures | NA | System displays options for sending disclosures: Electronic Delivery or Mail. | 718521_AC_07 |\n| Step 08 | Select "Electronic Delivery" as the disclosure delivery method. | Generate Disclosures | NA | System verifies 
user consent for electronic delivery and sends disclosures via eSign. | 718521_AC_08 |\n| Step 09 | Select "Mail" as 
the disclosure delivery method. | Generate Disclosures | NA | System sends disclosures via mail if user has not consented to electronic delivery. | 718521_AC_09 |\n| Step 10 | Navigate to "Manage Broker Disclosures" functionality. | Manage Broker Disclosures | NA | System displays options to append additional disclosures to the Newrez LE Package. | 
718521_AC_10 |\n| Step 11 | Select "Mortgage Broker Fee Agreement" from the list of additional disclosures. | Manage 
Broker Disclosures | NA | System displays a note indicating the form may not be suitable for every transaction or broker. | 718521_AC_11 |\n| Step 12 | Review license/registration disclosure requirements for the selected form. | Manage Broker Disclosures | NA | System appends the form to the Newrez LE Package if requirements are met. | 718521_AC_12 
|\n| Step 13 | Verify privilege restrictions for "Mortgage Broker Fee Agreement" and "Mortgage Broker License Type" fields. | Generate Disclosures | NA | System restricts access to these fields for users without privileges. | 718521_AC_13 |\n| Step 14 | Confirm compliance checks for appraisal orders and fee validations. | Generate Disclosures | NA | System performs compliance checks: Ignore 3rd Party Fee Check, Verify title fees after Loan Amount increase, Ignore 
Fee Quote Data validations. | 718521_AC_14 |\n\n---\n\nThis test case ensures validation of business rules, privilege restrictions, and compliance checks for generating disclosures in RTL channel loans.', 'WHL': '### Generated Test Case\n\n---\n\n**Test Case ID / Test Script ID:** 718521_WHL_01  \n**Test Scenario Id:** 718521_SC_01  \n**Test Scenario Description:** Validate disclosure generation and broker-related fields for WHL channel loans.  \n**Test Script Description:** Validate system behavior for generating disclosures, managing broker-related fields, and ensuring compliance checks for WHL channel loans.  \n**Pre-Condition & Assumptions:** Refer to provided precondition context.\n\n---\n\n### Test Steps\n\n| Test Step No. | Test Step Description
        | Screen Name                          | Test Data                  | Expected Results
                                                              | Requirement Mapping |\n|---------------|---------------------------------------------------------------------------------------|--------------------------------------|----------------------------|------------------------------------------------------------------------------------------------------|----------------------|\n| Step 01       | Navigate to "Generate Disclosures" section in H2O UI under HPMLDIS module.            | Generate Disclosures                 | NA                         | System displays available disclosure options: Intent to Proceed, Mortgage Broker Fee Agreement, Mortgage Broker License Type. | 718521_AC_01        |\n| Step 02       | Select "Intent to Proceed" from the disclosure options.                               | 
Generate Disclosures                 | NA                         | System allows appraisal order functionality. Compliance checks are performed: Ignore 3rd Party Fee Check, verify title fees after Loan Amount increase, ignore Fee Quote Data validations. | 718521_AC_02        |\n| Step 03       | Select "Mortgage Broker Fee Agreement" from the disclosure options.                   | Generate Disclosures                 | NA                         | System displays privilege-restricted access to the form. If user has privileges, form is included in the Newrez LE Package. | 718521_AC_03        |\n| Step 04       | Confirm inclusion of "Mortgage Broker Fee Agreement" in the Newrez LE Package.   
     | Generate Disclosures                 | NA                         | System appends the form to the Newrez LE Package. If user does not have privileges, access is restricted. | 718521_AC_04        |\n| Step 05       | Select "Mortgage Broker License Type" from the disclosure options.                    | Generate Disclosures                 | 
NA                         | System displays privilege-restricted access to the section. If SubPropState = CA, license section is displayed. | 718521_AC_05        |\n| Step 06       | Select the license type under which the loan will 
originate.                          | Generate Disclosures                 | License Type: DRE, RML, Other | System appends the selected license type to the Newrez LE Package. If user does not have privileges, access is restricted. | 
718521_AC_06        |\n| Step 07       | Select "Generate Disclosure" from the available options.
          | Generate Disclosures                 | NA                         | System displays options for sending disclosures. If user has consented to electronic delivery, disclosures are sent via eSign. If not, disclosures are sent via Mail. | 718521_AC_07        |\n| Step 08       | Navigate to "Manage Broker Disclosures" functionality.        
                        | Manage Broker Disclosures            | NA                         | System provides options to append additional disclosures to the Newrez LE Package. | 718521_AC_08        |\n| Step 09       | Select "Mortgage Broker Fee Agreement" from the list of additional disclosures.       | Manage Broker Disclosures            | NA  
                       | System displays a note indicating the form may not be suitable for every transaction or broker. User reviews license/registration disclosure requirements. | 718521_AC_09        |\n| Step 10       | Append "Mortgage Broker Fee Agreement" to the Newrez LE Package if requirements are met. | Manage Broker Disclosures
| NA                         | System appends the form to the Newrez LE Package if requirements are met. | 718521_AC_10        |\n| Step 11       | Select disclosure delivery method: Electronic Delivery or Mail.
| Generate Disclosures                 | Delivery Method: Electronic or Mail | If Electronic Delivery is selected, system verifies user consent for electronic disclosures. If Mail is selected, disclosures are sent via mail. | 718521_AC_11        |\n| Step 12       | Validate compliance checks for appraisal order functionality.
  | Generate Disclosures                 | NA                         | Compliance checks are performed: Ignore 3rd Party Fee Check, verify title fees after Loan Amount increase, ignore Fee Quote Data validations. | 718521_AC_12      
  |\n| Step 13       | Confirm privilege restrictions for "Mortgage Broker Fee Agreement" and "Mortgage Broker License Type." | Generate Disclosures                 | NA                         | System restricts access to privilege-restricted fields if user does not have required privileges. | 718521_AC_13        |\n| Step 14       | Validate state-specific logic for "Mortgage Broker License Type" field.               | Generate Disclosures                 | SubPropState: CA           | System displays "Mortgage Broker License Type" field only when SubPropState = CA. Logic confirmed by development team. | 718521_AC_14        |\n\n---\n\nThis test case ensures validation of system behavior for generating disclosures, managing broker-related fields, and enforcing compliance checks for WHL channel loans.', 'DTC': '### Test Case Generation\n\n**Test Case ID / Test Script ID:** 718521_DTC_01  \n**Test Scenario ID:** 718521_SC_01  \n**Test Scenario Description:** Validate the Generate Disclosures workflow for DTC channel loans, ensuring compliance with business rules and privilege restrictions.  \n**Test Script Description:** Validate the system behavior for generating disclosures, including field visibility, privilege restrictions, and compliance checks, for loans created under the DTC channel.  \n**Pre-Condition & Assumptions:** Refer to provided precondition context.  \n\n---\n\n| Test Step No. | Test Step Description                                                                 | Screen Name    
                 | Test Data                  | Expected Results
                                              | Requirement Mapping |\n|---------------|---------------------------------------------------------------------------------------|---------------------------------|----------------------------|--------------------------------------------------------------------------------------------------------------------|----------------------|\n| Step 01       | Navigate to the "Generate Disclosures" section in the H2O UI under the HPMLDIS module. | Generate Disclosures            | NA                         | System displays available disclosure options, excluding Mortgage Broker-related fields, as per DTC channel rules. | 718521_AC_01        |\n| Step 02   
    | Select the "Intent to Proceed" field from the disclosure options.                     | Generate Disclosures   
         | NA                         | System allows appraisal order functionality and performs compliance checks: Ignore 3rd Party Fee Check, verify title fees after Loan Amount increase, and ignore Fee Quote Data validations. | 718521_AC_02        |\n| Step 03       | Attempt to locate the "Mortgage Broker Fee/Compensation Agreement" field.      
       | Generate Disclosures            | NA                         | System does not display the "Mortgage Broker 
Fee/Compensation Agreement" field, as it is restricted for DTC channel loans. | 718521_AC_03        |\n| Step 04     
  | Attempt to locate the "Mortgage Broker License Type" field.                           | Generate Disclosures     
       | NA                         | System does not display the "Mortgage Broker License Type" field, as it is restricted for DTC channel loans.      | 718521_AC_04        |\n| Step 05       | Select "Generate Disclosure" from the available options.                              | Generate Disclosures            | NA                         | System displays options for sending disclosures: "Electronic Delivery" or "Mail".                                | 718521_AC_05        |\n| Step 06       | Select "Electronic Delivery" as the disclosure delivery method.
   | Generate Disclosures            | NA                         | System verifies that the user has consented to receive disclosures electronically. If consent is not provided, system displays an error message. | 718521_AC_06       
 |\n| Step 07       | Select "Mail" as the disclosure delivery method.                                      | Generate Disclosures            | NA                         | System sends disclosures via mail without requiring electronic consent.                                           | 718521_AC_07        |\n| Step 08       | Attempt to access the "Manage Broker Disclosures" functionality.                      | Manage Broker Disclosures       | NA
           | System does not display any Mortgage Broker-related disclosures, as they are restricted for DTC channel 
loans.    | 718521_AC_08        |\n| Step 09       | Confirm that the "Enable Electronic Disclosures" checkbox is unchecked by default.    | Loan Summary                    | NA                         | System displays the "Enable Electronic Disclosures" checkbox as unchecked by default.                            | 718521_AC_09        |\n| Step 10       | Check the "Enable Electronic Disclosures" checkbox.                                   | Loan Summary        
            | NA                         | System updates the checkbox state to checked and enables electronic disclosure functionality.                     | 718521_AC_10        |\n| Step 11       | Attempt to append additional disclosures to the Newrez LE Package.                    | Manage Broker Disclosures       | NA                         | 
System does not allow appending of Mortgage Broker-related disclosures, as they are restricted for DTC channel loans. | 718521_AC_11        |\n| Step 12       | Verify that compliance checks are performed after generating disclosures.              | Generate Disclosures            | NA                         | System executes compliance checks, including Ceypass Compliance Check and Title Fees Verification.                | 718521_AC_12        |\n\n---\n\nThis test case ensures that the system adheres to the business rules for the DTC channel, including the exclusion of Mortgage Broker-related fields and the enforcement of compliance checks.', 'CL1': '### Generated Test Case\n\n---\n\n**Test 
Case ID / Test Script ID:** 718521_CL1_01  \n**Test Scenario Id:** 718521_SC_01  \n**Test Scenario Description:** Validate business rules for generating disclosures in CL1 channel loans.  \n**Test Script Description:** Validate system behavior for disclosure generation, privilege-restricted fields, and compliance checks aligned to CL1 channel business rules.  \n**Pre-Condition & Assumptions:** Refer to provided precondition context.\n\n---\n\n### Test Steps\n\n| Test Step No. | Test Step Description                                                                 | Screen Name   
                       | Test Data                     | Expected Results
                                         | Requirement Mapping   |\n|---------------|---------------------------------------------------------------------------------------|--------------------------------------|-------------------------------|------------------------------------------------------------------------------------------------------|------------------------|\n| Step 01       | Navigate to "Generate Disclosures" section in H2O UI under HPMLDIS module.  
          | HPMLDIS > Generate Disclosures       | NA                            | System displays available disclosure options: Intent to Proceed, Mortgage Broker Fee Agreement, Mortgage Broker License Type. | 718521_AC_01          |\n| Step 02       | Select "Intent to Proceed" from the disclosure options.                               | HPMLDIS > Generate Disclosures       | NA                            | System allows appraisal order functionality. Compliance checks are performed: Ignore 3rd Party Fee Check, Verify Title Fees after Loan Amount increase, Ignore Fee Quote Data validations. | 718521_AC_02          |\n| Step 03       | Select "Mortgage Broker Fee Agreement" from the disclosure options.                   | HPMLDIS > Generate Disclosures       | NA                            | System displays privilege-restricted access to the form. If user has privileges, form is appended to the Newrez LE Package. If user 
does not have privileges, access is restricted. | 718521_AC_03          |\n| Step 04       | Select "Mortgage Broker 
License Type" from the disclosure options.                    | HPMLDIS > Generate Disclosures       | SubPropState = CA             | System displays privilege-restricted access to the section. If SubPropState = CA, license section is displayed. Dev team to confirm logic. | 718521_AC_04          |\n| Step 05       | Select the license type under which the loan will originate.                          | HPMLDIS > Generate Disclosures       | Options: DRE, RML, Other      | System appends the selected license type to the Newrez LE Package.                                  | 718521_AC_05          |\n| Step 06       | Select "Generate Disclosure" from the available options.
       | HPMLDIS > Generate Disclosures       | NA                            | System displays options for sending disclosures. If user has consented to electronic delivery, disclosures are sent via eSign. If user has not consented, 
disclosures are sent via Mail. | 718521_AC_06          |\n| Step 07       | Navigate to "Manage Broker Disclosures" functionality.                                | HPMLDIS > Manage Broker Disclosures  | NA                            | System provides options to append additional disclosures to the Newrez LE Package.                  | 718521_AC_07  
        |\n| Step 08       | Select "Mortgage Broker Fee Agreement" from the list of additional disclosures.       | 
HPMLDIS > Manage Broker Disclosures  | NA                            | System displays a note indicating the form may not be suitable for every transaction or broker. User reviews license/registration disclosure requirements. If requirements are met, form is appended to the Newrez LE Package. | 718521_AC_08          |\n| Step 09       | Select disclosure delivery method: Electronic Delivery or Mail.                       | HPMLDIS > Generate Disclosures       | Options: Electronic Delivery, Mail | If Electronic Delivery is selected, system verifies user consent for electronic disclosures. If Mail is selected, disclosures are sent via Mail. | 718521_AC_09          |\n| Step 10       | Validate 
compliance checks for appraisal order functionality.                         | HPMLDIS > Generate Disclosures       | NA                            | Compliance checks are executed: Ceypass Compliance Check, Ignore 3rd Party Fee Check, Verify Title Fees after Loan Amount increase. | 718521_AC_10          |\n\n---\n\n### Notes:\n1. **Privilege Restrictions:** Mortgage Broker Fee Agreement and Mortgage Broker License Type are privilege-restricted fields.  \n2. **State-Specific Logic:** Mortgage Broker License Type appears when SubPropState = CA. Logic confirmation required from development team.  \n3. **Compliance Checks:** Ensure compliance checks are performed for appraisal orders and fee validations.  \n\nThis test case aligns with enterprise QA standards and validates business rules for CL1 channel loans.'}
INFO:agents.excel_export_agent:Excel Export Agent started
INFO:agents.excel_export_agent:Sheets after cleanup: ['RTL', 'DTC', 'WHL', 'CL1']
INFO:agents.excel_export_agent:RTL -> Parsed 0 steps
INFO:agents.excel_export_agent:WHL -> Parsed 0 steps
INFO:agents.excel_export_agent:DTC -> Parsed 0 steps
INFO:agents.excel_export_agent:CL1 -> Parsed 0 steps
INFO:agents.excel_export_agent:Excel generated: output_excels\Indiv_US_718521_Test_Scripts_v1.0.xlsx

 Excel Generated at:
output_excels\Indiv_US_718521_Test_Scripts_v1.0.xlsx
