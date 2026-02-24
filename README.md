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
    'Content-Length': '73914'
    'api-key': 'REDACTED'
    'Accept': 'application/json;odata.metadata=none'
    'x-ms-client-request-id': 'cb10233b-1197-11f1-8436-7ced8dc28672'
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
    'request-id': 'cb10233b-1197-11f1-8436-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 15:45:07 GMT'
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
    'x-ms-client-request-id': 'ce060e9d-1197-11f1-9a3d-7ced8dc28672'
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
    'request-id': 'ce060e9d-1197-11f1-9a3d-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 15:45:12 GMT'
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
    'x-ms-client-request-id': 'cf54a3f8-1197-11f1-b4c6-7ced8dc28672'
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
    'request-id': 'cf54a3f8-1197-11f1-b4c6-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 15:45:14 GMT'
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
    'x-ms-client-request-id': 'd01be0aa-1197-11f1-92f1-7ced8dc28672'
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
    'request-id': 'd01be0aa-1197-11f1-92f1-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 15:45:15 GMT'
INFO:agents.retrieval_intelligence_agent:📊 Retrieved 21 docs for CL1
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:✅ Retrieval Completed
INFO:agents.llm_testcase_generator_agent:🤖 LLM Generator Running
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

### Precondition:
Create a loan from Customer Portal as per pre-conditions below:
1. **Channel**: RTL
2. **Loan Purpose**: Purchase
3. **Loan Type**: Conventional
4. **Product Code**: 30YRFXD
5. **Loan Stage**: Application

---

### Test Steps:

#### Step 1:
**Description**: Log in to H2O-A in UAT Environment.
**Screen**: Login Page
**Test Data**: https://uath2o.newrez.com
**Expected Result**: Login should be successful.

---

#### Step 2:
**Description**: Open the created loan which meets the pre-condition.
**Screen**: Loan Summary
**Test Data**: Loan ID created in precondition.
**Expected Result**: Loan Summary screen should be opened for the loan.

---

#### Step 3:
**Description**: Navigate to **DIS > Generate Disclosures**.
**Screen**: Generate Disclosures
**Test Data**: N/A
**Expected Result**: Generate Disclosures screen should be displayed.

---

#### Step 4:
**Description**: Verify the presence of the **Higher Priced Mortgage Loan (HPML)** field.
**Screen**: Generate Disclosures
**Test Data**: N/A
**Expected Result**: The **Higher Priced Mortgage Loan (HPML)** field should be present and allow selection.

---

#### Step 5:
**Description**: Verify the presence of the **Intent to Proceed** field.
**Screen**: Generate Disclosures
**Test Data**: N/A
**Expected Result**: The **Intent to Proceed** field should be present and allow selection.

---

#### Step 6:
**Description**: Verify the presence of the **Mortgage Broker Fee/Compensation Agreement** field.
**Screen**: Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data**: N/A
**Expected Result**: The **Mortgage Broker Fee/Compensation Agreement** field should be present and allow selection. 


---

#### Step 7:
**Description**: Verify the presence of the **Mortgage Broker License Type** field.
**Screen**: Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data**: SubPropState = CA
**Expected Result**: The **Mortgage Broker License Type** field should be present when **SubPropState = CA**.        

---

#### Step 8:
**Description**: Verify privilege restrictions for the **Mortgage Broker Fee/Compensation Agreement** field.
**Screen**: Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data**: User with restricted privileges.
**Expected Result**: The **Mortgage Broker Fee/Compensation Agreement** field should not be accessible to users with 
restricted privileges.

---

#### Step 9:
**Description**: Verify privilege restrictions for the **Mortgage Broker License Type** field.
**Screen**: Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data**: User with restricted privileges.
**Expected Result**: The **Mortgage Broker License Type** field should not be accessible to users with restricted privileges.

---

#### Step 10:
**Description**: Select **Yes** for the **Mortgage Broker Fee/Compensation Agreement** field and verify the inclusion in the Newrez LE Package.
**Screen**: Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data**: Select **Yes**.
**Expected Result**: The **Mortgage Broker Fee/Compensation Agreement** should be included in the Newrez LE Package. 


---

#### Step 11:
**Description**: Select the license type under which the loan will originate.
**Screen**: Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data**: Select **DRE** or **RML**.
**Expected Result**: The selected license type should be saved successfully.

---

#### Step 12:
**Description**: Verify that disclosures are appended to the Newrez LE Package.
**Screen**: Manage Broker Disclosures
**Test Data**: N/A
**Expected Result**: Disclosures should be appended to the Newrez LE Package successfully.

---

#### Step 13:
**Description**: Generate the disclosure package and verify successful generation.
**Screen**: Generate Disclosures
**Test Data**: N/A
**Expected Result**: Disclosure package should be generated successfully.

---

#### Step 14:
**Description**: Verify the delivery method for disclosures (e.g., **eSign** or **Mail**).
**Screen**: Generate Disclosures
**Test Data**: Select **eSign** or **Mail**.
**Expected Result**: Disclosures should be sent via the selected delivery method.

---

#### Step 15:
**Description**: Verify compliance checks (e.g., **Ceypass Compliance Check**, **Ignore 3rd Party Fee Check**, etc.).
**Screen**: Generate Disclosures
**Test Data**: N/A
**Expected Result**: Compliance checks should be executed successfully without errors.

---

#### Step 16:
**Description**: Verify that the **Intent to Proceed** is captured successfully.
**Screen**: Generate Disclosures
**Test Data**: Select **Yes** for Intent to Proceed.
**Expected Result**: The **Intent to Proceed** should be captured successfully.

---

#### Step 17:
**Description**: Log out of the application.
**Screen**: Logout
**Test Data**: N/A
**Expected Result**: User should be logged out successfully.

---

### Notes:
- Ensure all privilege restrictions are tested with appropriate user roles.
- Validate all fields for both presence and functionality.
- Verify that the **Mortgage Broker License Type** field appears only when **SubPropState = CA**.
- Ensure all compliance checks pass without errors.
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

### Precondition:
Create a loan from Broker Portal as per pre-conditions below:
1. Channel: Wholesale
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: Fixed30
5. Loan Stage: Application

---

### Test Steps:

#### Step 1: Log in to H2O-A in UAT Environment
**Screen:** Login Page
**Test Data:** https://uath2o.newrez.com
**Expected Result:** Login should be successful.

---

#### Step 2: Open the created loan which meets the Pre-Condition column
**Screen:** Loan Summary
**Test Data:** Loan ID created in precondition
**Expected Result:** Loan Summary screen should be opened for the loan.

---

#### Step 3: Navigate to **DIS > Generate Disclosures** and verify the following fields:
- **HPML**
- **Intent to Proceed**
- **Mortgage Broker Fee Agreement**
- **Mortgage Broker License Type**

**Screen:** DIS > Generate Disclosures
**Test Data:** Loan ID created in precondition
**Expected Result:**
- **HPML** field should be visible and functional.
- **Intent to Proceed** field should be visible and functional.
- **Mortgage Broker Fee Agreement** field should be visible and privilege restricted.
- **Mortgage Broker License Type** field should be visible when `SubPropState = CA` and privilege restricted.        

---

#### Step 4: Verify the functionality of **Generate Disclosure**
1. Select the **Intent to Proceed** checkbox.
2. Select **Mortgage Broker Fee/Compensation Agreement** as "Yes".
3. Verify that the **Mortgage Broker License Type** dropdown appears when `SubPropState = CA`.

**Screen:** DIS > Generate Disclosures
**Test Data:** Loan ID created in precondition
**Expected Result:**
- **Intent to Proceed** checkbox should be selectable.
- **Mortgage Broker Fee/Compensation Agreement** should be included in the Newrez LE Package when selected as "Yes". 

- **Mortgage Broker License Type** dropdown should appear when `SubPropState = CA`.

---

#### Step 5: Navigate to **Tools > Modernized Audit** and verify the audit entries for the following fields:
- **HPML**
- **Intent to Proceed**
- **Mortgage Broker Fee Agreement**
- **Mortgage Broker License Type**

**Screen:** Tools > Modernized Audit
**Test Data:** Loan ID created in precondition
**Expected Result:**
Audit entries should be created for the following fields:
- **HPML**:
  - User who made the change
  - Time Occurred
  - Previous Value
  - New Value
- **Intent to Proceed**:
  - User who made the change
  - Time Occurred
  - Previous Value
  - New Value
- **Mortgage Broker Fee Agreement**:
  - User who made the change
  - Time Occurred
  - Previous Value
  - New Value
- **Mortgage Broker License Type**:
  - User who made the change
  - Time Occurred
  - Previous Value
  - New Value

---

#### Step 6: Verify privilege restrictions for **Mortgage Broker Fee Agreement** and **Mortgage Broker License Type**
1. Log in with a user who does not have the required privileges.
2. Navigate to **DIS > Generate Disclosures**.

**Screen:** DIS > Generate Disclosures
**Test Data:** Loan ID created in precondition
**Expected Result:**
- **Mortgage Broker Fee Agreement** and **Mortgage Broker License Type** fields should not be visible for users without the required privileges.

---

#### Step 7: Verify the behavior when `SubPropState != CA`
1. Update the loan's `SubPropState` to a state other than CA.
2. Navigate to **DIS > Generate Disclosures**.

**Screen:** DIS > Generate Disclosures
**Test Data:** Loan ID created in precondition
**Expected Result:**
- **Mortgage Broker License Type** field should not appear when `SubPropState != CA`.

---

#### Step 8: Verify the **Generate Disclosure** button functionality
1. Fill in all required fields in **DIS > Generate Disclosures**.
2. Click on the **Generate Disclosure** button.

**Screen:** DIS > Generate Disclosures
**Test Data:** Loan ID created in precondition
**Expected Result:**
- Disclosures should be generated successfully.
- Confirmation message should appear indicating successful generation of disclosures.

---

#### Step 9: Verify audit entries for **Generate Disclosure** action
1. Navigate to **Tools > Modernized Audit**.
2. Verify the audit entry for the **Generate Disclosure** action.

**Screen:** Tools > Modernized Audit
**Test Data:** Loan ID created in precondition
**Expected Result:**
Audit entry should be created for the **Generate Disclosure** action with the following details:
- User who performed the action
- Time Occurred
- Action performed as "Generate Disclosure"

---

#### Step 10: Verify the **Electronic Delivery** functionality
1. Navigate to **Electronic Delivery**.
2. Verify the "Enable Electronic Disclosures" checkbox.

**Screen:** Electronic Delivery
**Test Data:** Loan ID created in precondition
**Expected Result:**
- "Enable Electronic Disclosures" checkbox should be visible and functional.
- User should be able to enable or disable the checkbox.

---

#### Step 11: Verify audit entries for **Electronic Delivery** changes
1. Navigate to **Tools > Modernized Audit**.
2. Verify the audit entry for changes made to the "Enable Electronic Disclosures" checkbox.

**Screen:** Tools > Modernized Audit
**Test Data:** Loan ID created in precondition
**Expected Result:**
Audit entry should be created for changes made to the "Enable Electronic Disclosures" checkbox with the following details:
- User who made the change
- Time Occurred
- Previous Value
- New Value
- Field name as "Enable Electronic Disclosures"

---

This test case ensures the functionality and audit tracking of the new fields added to Modernized Audit under **DIS > Generate Disclosures**.
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

### Precondition:
If Channel = DTC:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product Code: Any buydown product code
5. Loan Stage: Application

---

### Test Steps:

#### Step 1: Login to H2O-A in UAT Environment
**Description:** Log in to the H2O-A application in the UAT environment.
**Screen:** Login Page
**Test Data:** https://uath2o.newrez.com
**Expected Result:** Login should be successful.

---

#### Step 2: Open the created loan
**Description:** Open the loan created as per the precondition.
**Screen:** Loan Summary
**Test Data:** Loan ID created in precondition
**Expected Result:** Loan Summary screen should be opened for the loan.

---

#### Step 3: Verify Loan Details
**Description:** Navigate to Loan Details and verify the following fields:
- Loan Purpose
- Loan Product Code
- Buydown

**Screen:** Loan Summary
**Test Data:** N/A
**Expected Result:**
- Loan Purpose: Refinance
- Loan Product Code: Any buydown product code
- Buydown: Checkbox checked

---

#### Step 4: Verify Company Details
**Description:** Navigate to Company Details and verify the following fields:
- Loan Originator (Rep)
- Branch Name (Retail/DTC)

**Screen:** Loan Summary
**Test Data:** N/A
**Expected Result:**
- Loan Originator (Rep): Should display the assigned loan originator.
- Branch Name (Retail/DTC): Should display the branch name as Retail/DTC.

---

#### Step 5: Navigate to Modernized Audit
**Description:** Navigate to Tools > Modernized Audit and verify the following fields under "Generate Disclosures":  
1. **HPML**
   - Verify if the "Higher Priced Mortgage Loan" dropdown is present.
   - Expected Value: [Select...]

2. **Intent to Proceed**
   - Verify if the "Intent to Proceed" section is present.
   - Expected Value: Checkbox for "Allow Appraisal Order" should be present.

3. **Mortgage Broker Fee/Compensation Agreement**
   - Verify if the "Mortgage Broker Fee/Compensation Agreement" section is present.
   - Expected Value: Dropdown with "Yes/No" options.

4. **Mortgage Broker License Type**
   - Verify if the "Mortgage Broker License Type" section is present.
   - Expected Logic: Should appear when `SubPropState = CA`.

**Screen:** Modernized Audit > Generate Disclosures
**Test Data:** Loan created in precondition
**Expected Result:**
- All fields should be present as per the acceptance criteria.
- Privilege-restricted fields should only be visible to users with appropriate permissions.

---

#### Step 6: Validate Privilege Restrictions
**Description:** Validate that the following fields are privilege-restricted:
- Mortgage Broker Fee/Compensation Agreement
- Mortgage Broker License Type

**Screen:** Modernized Audit > Generate Disclosures
**Test Data:** User with restricted privileges
**Expected Result:**
- Restricted fields should not be visible to users without appropriate privileges.

---

#### Step 7: Verify Logic for Mortgage Broker License Type
**Description:** Validate that the "Mortgage Broker License Type" section appears only when `SubPropState = CA`.     
**Screen:** Modernized Audit > Generate Disclosures
**Test Data:** Loan with `SubPropState = CA`
**Expected Result:**
- "Mortgage Broker License Type" section should appear when `SubPropState = CA`.

---

#### Step 8: Generate Disclosure
**Description:** Generate the disclosure and verify the following:
- Disclosures are generated successfully.
- Verify the appended disclosures in the Newrez LE Package.

**Screen:** Modernized Audit > Generate Disclosures
**Test Data:** Loan created in precondition
**Expected Result:**
- Disclosures should be generated successfully.
- Appended disclosures should match the selected options.

---

#### Step 9: Validate Electronic Delivery Consent
**Description:** Verify that the user has consented to receive disclosures electronically.
**Screen:** Modernized Audit > Generate Disclosures
**Test Data:** Loan created in precondition
**Expected Result:**
- Checkbox for electronic delivery consent should be checked.
- If unchecked, disclosures should be sent via mail.

---

#### Step 10: Validate Additional Broker Disclosures
**Description:** Verify the functionality to append additional broker disclosures to the Newrez LE Package.
**Screen:** Modernized Audit > Manage Additional Broker Disclosures
**Test Data:** Loan created in precondition
**Expected Result:**
- Additional broker disclosures should be appended successfully.
- Count of appended disclosures should be displayed correctly.

---

### Notes:
- Ensure that all privilege-restricted fields are tested with both privileged and non-privileged users.
- Validate the logic for `SubPropState = CA` thoroughly with different states.
- Verify that all dropdowns and checkboxes function as expected.
- Ensure that the generated disclosures meet the business requirements.
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

### Precondition:
Create a loan from Broker Portal as per pre-conditions below:
1. **Channel**: CL1
2. **Loan Purpose**: Purchase
3. **Loan Type**: Conventional
4. **Product Code**: 30YRFXD
5. **Loan Stage**: Application

---

### Test Steps:

#### Step 01: Log in to H2O-A in UAT1 Environment
- **Screen**: Login Page
- **Test Data**: https://uath2o.newrez.com/
- **Expected Result**: Login should be successful.

#### Step 02: Navigate to "Create a New Loan"
- **Screen**: Create New Loan
- **Test Data**: N/A
- **Expected Result**: "Create New Loan" screen should be displayed.

#### Step 03: Assign Branch Name
- **Screen**: Create New Loan
- **Test Data**: Test Area (CL-TEST-REG-AREA)
- **Expected Result**: Branch should be assigned successfully.

#### Step 04: Select Loan Creation Method
- **Screen**: Create New Loan
- **Test Data**: Select "Create Loan with Servicing Data" under the 1003 Creation Method section.
- **Expected Result**: Loan creation method should be set to "Create Loan with Servicing Data."

#### Step 05: Verify Loan Type Field
- **Screen**: Create New Loan
- **Test Data**: N/A
- **Expected Result**: Loan's "Test Loan Type" field should be automatically set to "Temporary Test Loan."

#### Step 06: Navigate to "Generate Disclosures"
- **Screen**: Loan Summary
- **Test Data**: N/A
- **Expected Result**: "Generate Disclosures" screen should be displayed.

#### Step 07: Verify "Higher Priced Mortgage Loan (HPML)" Field
- **Screen**: Generate Disclosures
- **Test Data**: N/A
- **Expected Result**: "Higher Priced Mortgage Loan" field should be present and allow selection of Yes/No.

#### Step 08: Verify "Intent to Proceed" Field
- **Screen**: Generate Disclosures
- **Test Data**: N/A
- **Expected Result**: "Intent to Proceed" field should be present and allow selection of Yes/No.

#### Step 09: Verify "Mortgage Broker Fee/Compensation Agreement" Field
- **Screen**: Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
- **Test Data**: N/A
- **Expected Result**: "Mortgage Broker Fee/Compensation Agreement" field should be present and allow selection of Yes/No.

#### Step 10: Verify "Mortgage Broker License Type" Field
- **Screen**: Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
- **Test Data**: SubPropState = CA
- **Expected Result**: "Mortgage Broker License Type" field should appear when SubPropState = CA.

#### Step 11: Verify Privilege Restrictions
- **Screen**: Generate Disclosures
- **Test Data**: User with restricted privileges
- **Expected Result**: Fields "Mortgage Broker Fee/Compensation Agreement" and "Mortgage Broker License Type" should 
only be visible to users with appropriate privileges.

#### Step 12: Generate Disclosure
- **Screen**: Generate Disclosures
- **Test Data**: Select all required fields and click "Generate Disclosure."
- **Expected Result**: Disclosure should be generated successfully.

#### Step 13: Verify Disclosure Delivery Method
- **Screen**: Generate Disclosures
- **Test Data**: Select "Send via eSign" or "Send via Mail."
- **Expected Result**: Disclosure delivery method should be set as per the selection.

#### Step 14: Verify Additional Broker Disclosures
- **Screen**: Manage Additional Broker Disclosures
- **Test Data**: Append additional disclosures to the Newrez LE Package.
- **Expected Result**: Additional disclosures should be appended successfully.

#### Step 15: Validate Final Disclosure Package
- **Screen**: Loan Summary
- **Test Data**: N/A
- **Expected Result**: Final disclosure package should include all selected fields and appended disclosures.

---

### Notes:
- Ensure that privilege restrictions are tested with both privileged and non-privileged users.
- Verify that the "Mortgage Broker License Type" field appears only when SubPropState = CA.
- Validate that all fields are functioning as expected and meet the acceptance criteria.
INFO:agents.llm_testcase_generator_agent:✅ LLM Generation Completed
INFO:agents.excel_export_agent:Excel Export Agent started
INFO:agents.excel_export_agent:RTL -> Parsed 17 steps
INFO:agents.excel_export_agent:WHL -> Parsed 11 steps
INFO:agents.excel_export_agent:DTC -> Parsed 10 steps
INFO:agents.excel_export_agent:CL1 -> Parsed 15 steps
INFO:agents.excel_export_agent:Excel generated: output_excels\Indiv_US_718521_Test Scripts_v1.0.xlsx

 Excel Generated at:
output_excels\Indiv_US_718521_Test Scripts_v1.0.xlsx
(.venv) PS C:\Users\h84609n\Desktop\AgenticAI> 
