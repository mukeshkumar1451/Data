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
    'x-ms-client-request-id': '565a3c29-1196-11f1-966c-7ced8dc28672'
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
    'request-id': '565a3c29-1196-11f1-966c-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 15:34:42 GMT'
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
    'x-ms-client-request-id': '57e01f46-1196-11f1-a1b3-7ced8dc28672'
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
    'request-id': '57e01f46-1196-11f1-a1b3-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 15:34:44 GMT'
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
    'x-ms-client-request-id': '598fcbcf-1196-11f1-8367-7ced8dc28672'
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
    'request-id': '598fcbcf-1196-11f1-8367-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 15:34:46 GMT'
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
    'x-ms-client-request-id': '5af5652f-1196-11f1-a118-7ced8dc28672'
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
    'request-id': '5af5652f-1196-11f1-a118-7ced8dc28672'
    'elapsed-time': 'REDACTED'
    'Date': 'Tue, 24 Feb 2026 15:34:49 GMT'
INFO:agents.retrieval_intelligence_agent:📊 Retrieved 21 docs for CL1
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"
INFO:agents.retrieval_intelligence_agent:✅ Retrieval Completed
INFO:agents.llm_testcase_generator_agent:🤖 LLM Generator Running
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

### Precondition:
Create a loan from Customer Portal as per pre-conditions below:
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: 30YRFXD
5. Loan Stage: Application

---

### Test Steps:

#### Step 01:
**Description:** Log in to H2O-A in UAT1 Environment.
**Screen:** Login Page
**Test Data:** https://uath2o.newrez.com/
**Expected Result:** Login should be successful.

---

#### Step 02:
**Description:** Create a new loan in the system.
**Screen:** Create New Loan
**Test Data:** N/A
**Expected Result:** Create New Loan screen should open.

---

#### Step 03:
**Description:** Select the Branch Name under the Business Unit as per pre-condition.
**Screen:** Create New Loan
**Test Data:** Retail URLA Test Branch 2 (RET-testURLA2)
**Expected Result:** Branch should be assigned successfully.

---

#### Step 04:
**Description:** Select the 'Create Loan By' option as Import DU 3.2 File or MISMO 3.4/iLAD File under the 1003 Creation Method section.
**Screen:** Create New Loan
**Test Data:** N/A
**Expected Result:** 'Create Loan By' should be selected as Import DU 3.2 File or MISMO 3.4/iLAD File.

---

#### Step 05:
**Description:** Verify the 'Test Loan Type' field.
**Screen:** Create New Loan
**Test Data:** N/A
**Expected Result:** Loan's "Test Loan Type" field should be automatically populated.

---

#### Step 06:
**Description:** Navigate to **DIS > Generate Disclosures**.
**Screen:** Generate Disclosures
**Test Data:** N/A
**Expected Result:** Generate Disclosures screen should open successfully.

---

#### Step 07:
**Description:** Verify the **Higher Priced Mortgage Loan (HPML)** field is displayed.
**Screen:** Generate Disclosures
**Test Data:** N/A
**Expected Result:** HPML field should be displayed successfully.

---

#### Step 08:
**Description:** Verify the **Intent to Proceed** field is displayed.
**Screen:** Generate Disclosures
**Test Data:** N/A
**Expected Result:** Intent to Proceed field should be displayed successfully.

---

#### Step 09:
**Description:** Verify the **Mortgage Broker Fee/Compensation Agreement** field is displayed.
**Screen:** Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data:** N/A
**Expected Result:** Mortgage Broker Fee/Compensation Agreement field should be displayed successfully.

---

#### Step 10:
**Description:** Verify the **Mortgage Broker License Type** field is displayed when SubPropState = CA.
**Screen:** Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data:** SubPropState = CA
**Expected Result:** Mortgage Broker License Type field should be displayed successfully when SubPropState = CA.     

---

#### Step 11:
**Description:** Verify privilege restrictions for the **Mortgage Broker Fee/Compensation Agreement** field.
**Screen:** Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data:** N/A
**Expected Result:** Field should be privilege-restricted and only accessible to authorized users.

---

#### Step 12:
**Description:** Verify privilege restrictions for the **Mortgage Broker License Type** field.
**Screen:** Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data:** N/A
**Expected Result:** Field should be privilege-restricted and only accessible to authorized users.

---

#### Step 13:
**Description:** Verify the functionality to append additional disclosures to the Newrez LE Package.
**Screen:** Manage Broker Disclosures
**Test Data:** N/A
**Expected Result:** Additional disclosures should be appended to the Newrez LE Package successfully.

---

#### Step 14:
**Description:** Verify the **Do you want to include Mortgage Broker Fee/Compensation Agreement in the Newrez LE Package?** field.
**Screen:** Manage Broker Disclosures
**Test Data:** Yes/No
**Expected Result:** Field should allow the user to select Yes or No and save the selection successfully.

---

#### Step 15:
**Description:** Verify the **Under which license will you originate this loan?** field.
**Screen:** Manage Broker Disclosures
**Test Data:** DRE/RML
**Expected Result:** Field should allow the user to select the appropriate license type (DRE/RML).

---

#### Step 16:
**Description:** Verify that disclosures are appended to the Newrez LE Package.
**Screen:** Manage Broker Disclosures
**Test Data:** N/A
**Expected Result:** Disclosures should be appended to the Newrez LE Package successfully.

---

#### Step 17:
**Description:** Verify the **Generate Disclosure** button functionality.
**Screen:** Generate Disclosures
**Test Data:** N/A
**Expected Result:** Disclosures should be generated successfully upon clicking the button.

---

#### Step 18:
**Description:** Verify the **Intent to Proceed** button functionality.
**Screen:** Generate Disclosures
**Test Data:** N/A
**Expected Result:** Intent to Proceed should be successfully recorded upon clicking the button.

---

#### Step 19:
**Description:** Verify that disclosures are sent via the selected delivery method (eSign or Mail).
**Screen:** Generate Disclosures
**Test Data:** eSign/Mail
**Expected Result:** Disclosures should be sent via the selected delivery method.

---

#### Step 20:
**Description:** Verify the **Ceypass Compliance Check** functionality.
**Screen:** Generate Disclosures
**Test Data:** N/A
**Expected Result:** Ceypass Compliance Check should be executed successfully.

---

#### Step 21:
**Description:** Verify the **Ignore 3rd Party Fee Check** functionality.
**Screen:** Generate Disclosures
**Test Data:** N/A
**Expected Result:** Ignore 3rd Party Fee Check should be executed successfully.

---

#### Step 22:
**Description:** Verify the **Ignore Fee Quote Data Validations** functionality.
**Screen:** Generate Disclosures
**Test Data:** N/A
**Expected Result:** Ignore Fee Quote Data Validations should be executed successfully.

---

#### Step 23:
**Description:** Verify the **Title Fees Verified after LA Increase** functionality.
**Screen:** Generate Disclosures
**Test Data:** N/A
**Expected Result:** Title Fees Verified after LA Increase should be executed successfully.

---

### End of Test Steps
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

#### Step 01
**Description:** Log in to UAT1 H2O-A
**Screen:** Login page
**Test Data:** https://uath2o.newrez.com/
**Expected Result:** Login should be successful

#### Step 02
**Description:** Open a loan created as per the preconditions.
**Screen:** Dashboard
**Test Data:** Loan ID from precondition
**Expected Result:** Loan should be opened and Loan summary page should be displayed.

#### Step 03
**Description:** Navigate to `DIS > Generate Disclosures` screen.
**Screen:** Generate Disclosures
**Test Data:** Loan ID from precondition
**Expected Result:** Generate Disclosures screen should be displayed.

#### Step 04
**Description:** Verify the presence of the `Higher Priced Mortgage Loan (HPML)` field.
**Screen:** Generate Disclosures
**Test Data:** N/A
**Expected Result:** `Higher Priced Mortgage Loan (HPML)` field should be present and allow selection.

#### Step 05
**Description:** Verify the presence of the `Intent to Proceed` field.
**Screen:** Generate Disclosures
**Test Data:** N/A
**Expected Result:** `Intent to Proceed` field should be present and allow selection.

#### Step 06
**Description:** Verify the presence of the `Mortgage Broker Fee/Compensation Agreement` field.
**Screen:** Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data:** N/A
**Expected Result:** `Mortgage Broker Fee/Compensation Agreement` field should be present and allow selection.       

#### Step 07
**Description:** Verify the presence of the `Mortgage Broker License Type` field when `SubPropState = CA`.
**Screen:** Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data:** SubPropState = CA
**Expected Result:** `Mortgage Broker License Type` field should be displayed when `SubPropState = CA`.

#### Step 08
**Description:** Verify privilege restrictions for `Mortgage Broker Fee/Compensation Agreement` and `Mortgage Broker 
License Type`.
**Screen:** Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data:** User without required privileges
**Expected Result:** Fields should not be accessible to users without required privileges.

#### Step 09
**Description:** Select `Yes` for `Do you want to include Mortgage Broker Fee/Compensation Agreement in the Newrez LE Package?`
**Screen:** Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
**Test Data:** Select `Yes`
**Expected Result:** `Mortgage Broker Fee/Compensation Agreement` should be included in the Newrez LE Package.       

#### Step 10
**Description:** Save the changes and verify the audit log for the updated fields.
**Screen:** Audit
**Test Data:** Field names: `HPML`, `Intent to Proceed`, `Mortgage Broker Fee/Compensation Agreement`, `Mortgage Broker License Type`
**Expected Result:** Audit log should display the updated fields with User, Time Occurred, Previous Value, and New Value.

#### Step 11
**Description:** Verify that disclosures are generated successfully with the selected fields.
**Screen:** Generate Disclosures
**Test Data:** N/A
**Expected Result:** Disclosures should be generated successfully with the selected fields included.

---

### Notes:
- Ensure that all privilege-restricted fields are tested with both privileged and non-privileged users.
- Validate the logic for `Mortgage Broker License Type` field appearance when `SubPropState = CA` with the development team.
- Verify that all fields are correctly reflected in the audit log.
INFO:httpx:HTTP Request: POST https://centralus.api.cognitive.microsoft.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-12-01-preview "HTTP/1.1 200 OK"

===== GENERATED OUTPUT =====

### Precondition:
If Channel = DTC:
Create a loan from Ignite Portal as per pre-conditions below:
1. **Channel**: DTC
2. **Loan Purpose**: Refinance
3. **Loan Type**: Conventional
4. **Product Code**: Any buydown product code
5. **Loan Stage**: Application

---

### Test Steps:

#### **Step 1**: Log in to H2O-A in UAT Environment
- **Screen**: Login Page
- **Test Data**: https://uath2o.newrez.com
- **Expected Result**: Login should be successful.

---

#### **Step 2**: Open the created loan which meets the precondition
- **Screen**: Loan Summary
- **Test Data**: Loan ID created in precondition
- **Expected Result**: Loan Summary screen should be opened for the loan.

---

#### **Step 3**: Navigate to Loan Details and verify the following fields:
- **Screen**: Loan Summary
- **Test Data**: N/A
- **Expected Result**: Below fields should be present with values:
  - **Loan Purpose**: Refinance
  - **Loan Product Code**: Any buydown product code
  - **Buydown**: Checkbox checked

---

#### **Step 4**: Navigate to Company Details and verify the following fields:
- **Screen**: Loan Summary
- **Test Data**: N/A
- **Expected Result**: Below fields should be present with values:
  - **Loan Originator (Rep)**: Valid Loan Originator name
  - **Branch Name (Retail/DTC)**: DTC

---

#### **Step 5**: Navigate to Tools > Modernized Audit and verify the following fields:
- **Screen**: Modernized Audit
- **Test Data**: N/A
- **Expected Result**: Below fields should be present in the **Generate Disclosures** section:
  - **Higher Priced Mortgage Loan (HPML)**: Dropdown with valid options
  - **Intent to Proceed**: Checkbox
  - **Mortgage Broker Fee Agreement**: Checkbox
  - **Mortgage Broker License Type**: Dropdown (should appear only when `SubPropState = CA`)

---

#### **Step 6**: Verify privilege restrictions for the following fields:
- **Screen**: Modernized Audit
- **Test Data**: User with restricted privileges
- **Expected Result**: Below fields should be restricted based on user privileges:
  - **Mortgage Broker Fee Agreement**
  - **Mortgage Broker License Type**

---

#### **Step 7**: Verify the logic for **Mortgage Broker License Type** visibility
- **Screen**: Modernized Audit
- **Test Data**: Loan with `SubPropState = CA`
- **Expected Result**:
  - **Mortgage Broker License Type** dropdown should appear when `SubPropState = CA`.
  - Dropdown should not appear for other states.

---

#### **Step 8**: Generate Disclosure and verify the following:
- **Screen**: Modernized Audit > Generate Disclosure
- **Test Data**: N/A
- **Expected Result**:
  - Disclosure should be generated successfully.
  - Verify the following options:
    - **Allow Appraisal Order**: Checkbox
    - **Ceypass Compliance Check**: Checkbox
    - **Ignore 3rd Party Fee Check**: Checkbox
    - **Title Fees Verified after LA Increase**: Checkbox
    - **Ignore Fee Quote Data Validations**: Checkbox

---

#### **Step 9**: Verify the inclusion of **Mortgage Broker Fee/Compensation Agreement** in the Newrez LE Package     
- **Screen**: Modernized Audit > Generate Disclosure
- **Test Data**: Select "Yes" for **Do you want to include Mortgage Broker Fee/Compensation Agreement in the Newrez LE Package?**
- **Expected Result**:
  - **Mortgage Broker Fee/Compensation Agreement** should be included in the Newrez LE Package.
  - Verify the following:
    - **Under which license will you originate this loan?**: Dropdown with valid license options (e.g., DRE, RML).   

---

#### **Step 10**: Verify Manage Additional Broker Disclosures functionality
- **Screen**: Modernized Audit > Manage Broker Disclosures
- **Test Data**: Add additional disclosures
- **Expected Result**:
  - Additional disclosures should be appended to the Newrez LE Package.
  - Verify the count of disclosures appended.

---

#### **Step 11**: Verify electronic delivery consent
- **Screen**: Modernized Audit > Generate Disclosure
- **Test Data**: Select "Yes" for **Have consented to receive disclosures electronically**
- **Expected Result**:
  - Disclosures should be sent via eSign.
  - If "No" is selected, disclosures should be sent via Mail.

---

#### **Step 12**: Verify Higher Priced Mortgage Loan (HPML) field
- **Screen**: Modernized Audit > Generate Disclosure
- **Test Data**: Select valid options for HPML
- **Expected Result**:
  - HPML field should allow valid selections.
  - Verify the behavior when HPML is selected or not selected.

---

### Notes:
- Ensure all privilege-restricted fields are tested with both privileged and non-privileged users.
- Verify the logic for `SubPropState = CA` thoroughly with different state values.
- Validate the inclusion of disclosures in the Newrez LE Package for all scenarios.

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

---

#### Step 02: Create a New Loan
- **Screen**: Create New Loan
- **Test Data**: N/A
- **Expected Result**: Create New Loan screen should open.

---

#### Step 03: Assign Branch Name
- **Screen**: Create New Loan
- **Test Data**: Test Area (CL-TEST-REG-AREA)
- **Expected Result**: Branch should be assigned successfully.

---

#### Step 04: Select Loan Creation Method
- **Screen**: Create New Loan
- **Test Data**: Create Loan with Servicing Data
- **Expected Result**: Loan creation method should be selected as "Create Loan with Servicing Data."

---

#### Step 05: Verify Loan Type Field
- **Screen**: Create New Loan
- **Test Data**: N/A
- **Expected Result**: Loan's "Test Loan Type" field should be automatically set to "Temporary Test Loan."

---

#### Step 06: Navigate to Generate Disclosures
- **Screen**: Loan Details Page
- **Test Data**: N/A
- **Expected Result**: User should be able to navigate to the "Generate Disclosures" section.

---

#### Step 07: Verify Higher Priced Mortgage Loan (HPML) Field
- **Screen**: Generate Disclosures
- **Test Data**: N/A
- **Expected Result**: "Higher Priced Mortgage Loan" field should be available for selection.

---

#### Step 08: Verify Intent to Proceed Field
- **Screen**: Generate Disclosures
- **Test Data**: N/A
- **Expected Result**: "Intent to Proceed" field should be available for selection.

---

#### Step 09: Verify Mortgage Broker Fee/Compensation Agreement Field
- **Screen**: Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
- **Test Data**: N/A
- **Expected Result**: "Mortgage Broker Fee/Compensation Agreement" field should be available for selection.

---

#### Step 10: Verify Mortgage Broker License Type Field
- **Screen**: Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
- **Test Data**: SubPropState = CA
- **Expected Result**: "Mortgage Broker License Type" field should appear when SubPropState is set to CA.

---

#### Step 11: Verify Privilege Restrictions
- **Screen**: Generate Disclosures
- **Test Data**: User privileges
- **Expected Result**: Fields "Mortgage Broker Fee/Compensation Agreement" and "Mortgage Broker License Type" should 
be privilege-restricted.

---

#### Step 12: Generate Disclosure
- **Screen**: Generate Disclosures
- **Test Data**: Select all required fields and click "Generate Disclosure."
- **Expected Result**: Disclosure should be generated successfully.

---

#### Step 13: Verify Electronic Delivery Consent
- **Screen**: Generate Disclosures
- **Test Data**: Select "Yes" for electronic delivery consent.
- **Expected Result**: Disclosure should be sent electronically.

---

#### Step 14: Verify Disclosure Delivery via Mail
- **Screen**: Generate Disclosures
- **Test Data**: Select "No" for electronic delivery consent.
- **Expected Result**: Disclosure should be sent via mail.

---

#### Step 15: Verify Additional Disclosures Management
- **Screen**: Manage Additional Broker Disclosures
- **Test Data**: Append additional disclosures to the Newrez LE Package.
- **Expected Result**: Additional disclosures should be appended successfully.

---

#### Step 16: Verify Final Disclosure Package
- **Screen**: Disclosure Package
- **Test Data**: N/A
- **Expected Result**: Final disclosure package should include all selected fields and appended disclosures.

---

### Notes:
- Ensure that privilege-restricted fields are tested with both privileged and non-privileged users.
- Verify that the logic for "Mortgage Broker License Type" field appearance is implemented correctly (SubPropState = 
CA).
- Validate all fields for accuracy and alignment with business requirements.
INFO:agents.llm_testcase_generator_agent:✅ LLM Generation Completed
INFO:agents.excel_export_agent:Excel Export Agent started
INFO:agents.excel_export_agent:RTL -> Parsed 23 steps
INFO:agents.excel_export_agent:WHL -> Parsed 11 steps
INFO:agents.excel_export_agent:DTC -> Parsed 12 steps
INFO:agents.excel_export_agent:CL1 -> Parsed 16 steps
INFO:agents.excel_export_agent:Excel generated: output_excels\Indiv_US_718521_Test Scripts_v1.0.xlsx

 Excel Generated at:
output_excels\Indiv_US_718521_Test Scripts_v1.0.xlsx
(.venv) PS C:\Users\h84609n\Desktop\AgenticAI>


















