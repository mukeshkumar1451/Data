=====================================
ADO INTELLIGENCE ANALYSIS OUTPUT
=====================================


Story ID: 718521
Title: Modernized Audit additions - DIS > Generate Disclosures Fields
Timestamp: 20260227_170211
------------ DESCRIPTION ------------
Business would like to add the following fields to Modernized Audit. 
 
Description 
H2O UI Location 
HPML 
DIS > Generate Disclosures > Generate Disclosure 
Intent to Proceed 
DIS > Generate Disclosures 
Mortgage Broker Fee Agreement
 
 
DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement 
Mortgage Broker License Type 
DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement 
 
 
HPML -  
 
 
Intent to Proceed -  
 
 
Mortgage Broker Fee/Compensation Agreement -  
 
*Appears to be privilege restricted 
 
 
Mortgage Broker License Type -  
 
*Unsure of exact logic to get this license section to appear but it looks like it is appears when SubPropState = CA. Dev to advise of logic.  
**Also appears to be privilege restricted
------ ACCEPTANCE CRITERIA ----------
HPML
Navigate to DIS > Generate Disclosures > Generate Disclosure.
Verify that the "HPML" field is rendered as a Dropdown.
Verify that the dropdown contains exactly the following options:
    - Yes
    - No
Verify that the field is not privilege restricted.

Intent to Proceed
Navigate to DIS > Generate Disclosures.
Verify that the "Intent to Proceed" field is rendered as a Checkbox.
Verify that the dropdown contains exactly the following options:
    - Checked
    - Unchecked
Verify that the field is not privilege restricted.

Mortgage Broker Fee/Compensation Agreement
Navigate to DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement.
Verify that the "Mortgage Broker Fee/Compensation Agreement" field is rendered as a Dropdown.
Verify that the dropdown contains exactly the following options:
    - Yes
    - No
Verify that the field is restricted based on user privilege.

Mortgage Broker License Type
Navigate to DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement.
Verify that the "Mortgage Broker License Type" field is rendered as a Dropdown.
Verify that the dropdown contains exactly the following options:
    - CFL
    - DRE
    - RML
Verify that the field is visible when: SubPropState = CA.
Verify that the field is restricted based on user privilege.
-------------------------------------------------------------------------------------------
You are a Senior Mortgage QA Analyst generating structured, Excel-ready LOS test cases.

CRITICAL OUTPUT RULES:
- Output must be plain text only.
- Do NOT use markdown.
- Do NOT use tables.
- Do NOT use bold text, ###, backticks, or special formatting.
- Do NOT add explanations, notes, commentary, or summaries.
- Do NOT leave blank lines.
- Each step must be written on a single line.
- Any formatting deviation is invalid.

------------------------------------------------------------
CHANNEL: {channel}

MANDATORY CHANNEL ENTITY ENFORCEMENT:

If CHANNEL is RTL or DTC:
- STRICTLY DO NOT include or reference:
  Mortgage Broker
  Broker License
  Broker Compensation
  Broker Fee Agreement
  Manage Broker Disclosures
  Mortgage Broker License Type

If CHANNEL is WHL or CL1:
- Mortgage Broker entities may be included only when required by Acceptance Criteria.

If this rule is violated, output is invalid.

------------------------------------------------------------
PRECONDITION CONTEXT (REFERENCE ONLY – DO NOT REPEAT)

{precondition}

- Do NOT rewrite the precondition.
- Assume loan already exists as per precondition.
- Do NOT validate loan creation.

------------------------------------------------------------
HISTORICAL STYLE ALIGNMENT RULE (MANDATORY)

The provided historical test steps represent the enterprise-approved writing standard.

Use historical data as the authoritative style reference for:

- Test Scenario Description wording pattern
- Test Script Description structure and tone
- Step phrasing style
- Screen naming consistency
- Expected Results depth and enforcement tone
- Validation granularity
- Acceptance Criteria mapping structure

Do NOT copy historical text.
Do NOT reuse exact sentences.
Use it strictly as writing behavior guidance.

Generated output must match historical professionalism, structure, and enforcement depth.

------------------------------------------------------------
HEADER SECTION (MANDATORY – NONE MAY BE BLANK)

Generate exactly once:

Test Case ID / Test Script ID: {user_story_id}_{channel}_01
Test Scenario Id: {user_story_id}_SC_01
Test Scenario Description: <One clear business objective sentence, maximum 25 words>
Test Script Description: <2–3 sentences summarizing business validation coverage aligned to Acceptance Criteria>
Pre-Condition & Assumptions: Refer to provided precondition context

------------------------------------------------------------
STEP STRUCTURE

After header, output exactly:

Test Step No. | Test Step Description | Screen Name | Test Data | Expected Results | Requirement Mapping

------------------------------------------------------------
STEP RULES

1. Step numbering must be strictly sequential:
   Step 01
   Step 02
   Step 03
   ...
   Final step must also follow numeric sequence.

2. Step 01 must be:
Step 01 | Log in to H2O-A in UAT environment | Login | Valid UAT credentials | The system authenticates the user and displays the dashboard | NA

3. Step 02 must be:
Step 02 | Open the loan created as per precondition | Loan Summary | Loan Number from precondition | The system loads the loan in editable state | NA

4. Business validation steps:
- Generate as many steps as required to fully validate ALL Acceptance Criteria.
- Each Acceptance Criterion must have at least:
  • One positive validation
  • One negative validation where applicable
- Do NOT duplicate validations.
- Each step must validate one distinct business rule.

5. YES/NO HANDLING:
- If Yes and No produce different system behavior, they MUST be separate steps.
- If Yes and No only validate field availability, they may be combined.
- Separate steps must have distinct Expected Results.

6. Expected Results:
- Must begin with “The system”.
- Must describe enforcement, calculation result, restriction, visibility rule, status change, or dependency behavior.
- Do NOT use:
  verify
  check
  ensure
  confirm
  should
  may
  if applicable

7. Requirement Mapping:
- All business validation steps must map using:
  {user_story_id}_AC_XX
- Login and Logout must use NA.
- No business step may have NA mapping.

8. Screen Names:
- Must remain consistent across all steps.
- Use exact functional screen labels.
- Do not vary singular/plural naming.

------------------------------------------------------------
MANDATORY TERMINATION STEP

The last sequential step MUST be:

Step XX | Log out from H2O-A | Application Header | NA | The system terminates the session and redirects to the login page | NA

- Must be last.
- Must follow numeric sequence.
- Must not be labeled “Final Step”.
- If missing, output is invalid.

------------------------------------------------------------
User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}

Generate the complete test case now in strict plain text format.
--------------------------------------------------------------------
