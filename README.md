(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> py test_rag.py

🚀 RAG Test Case Generation Started

📥 Loading user story YAML...
✅ YAML loaded

🔧 Initializing RAG Retriever...
✅ Retriever ready

🔍 Running vector search in Azure AI Search...

🔹 Step 1: Detecting channels from AC

🔍 Detecting channels from Acceptance Criteria...

🧠 Raw detected channels: {'WHL'}
✅ Final channels after rule mapping: {'CL1', 'WHL'}

🔎 Channel Filter: channel eq 'CL1' or channel eq 'WHL'

🔹 Step 2: Preparing semantic query text
🧠 Creating embedding from User Story + Description + AC...
✅ Embedding length: 3072

🔹 Step 3: Sending vector search to Azure AI Search
✅ Retrieved 40 chunks from vector DB

✅ Retrieved 40 vector chunks

🧩 Rebuilding historical testcases from chunks...
   ↳ Rebuilding TestCase: 740128_CL1_01
🧩 Rebuilding full testcase for: 740128_CL1_01
   ↳ Rebuilding TestCase: 740128_WHL_01
🧩 Rebuilding full testcase for: 740128_WHL_01
   ↳ Rebuilding TestCase: 749011_WHL_02
🧩 Rebuilding full testcase for: 749011_WHL_02
   ↳ Rebuilding TestCase: 749011_WHL_01
🧩 Rebuilding full testcase for: 749011_WHL_01
   ↳ Rebuilding TestCase: 749011_WHL_03
🧩 Rebuilding full testcase for: 749011_WHL_03
✅ Context ready for LLM

🤖 Sending context to Azure OpenAI for test case generation...

✅ LLM Response Received

----- GENERATED OUTPUT PREVIEW -----

Scenario: Verify "Is the Property a Condo?" defaults to YES for Detached Condominium       
Script: FHA_CaseNumber_Default_Yes_DetachedCondo
Precondition: Loan is a Wholesale loan, FHA Request Case Number screen is available, and Type of Property is set to "Detached Condominium".
Requirement: AC1

Step 01 | Log into H2OA in UAT environment | Login Page | https://uath2o.newrez.com/ | Login should be successful
Step 02 | Open the loan created as per preconditions | Loan Summary | nan | Loan Summary screen should be opened
Step 03 | Navigate to Status > FHA Request Case Number | FHA Request Case Number | nan | FHA Request Case Number screen should be opened
Step 04 | Verify "Is the Property a Condo?" field in the Type of Case section | FHA Request Case Number | nan | Default value shou

-----------------------------------
