Expected Output Format

Test Case ID / Test Script ID: {user_story_id}_{channel}_01
Test Scenario Id: {user_story_id}_SC_01
Test Scenario Description: One sentence describing the business objective (max 25 words)
Test Script Description: 2–3 sentences summarizing validations performed
Pre-Condition & Assumptions: Refer to provided precondition context

Test Step No. | Test Step Description | Screen Name | Test Data | Expected Results | Requirement Mapping

Step 01 | Log in to H2O-A in UAT environment | Login | Valid UAT credentials | The system authenticates the user and displays the dashboard | NA

Step 02 | Open the loan created as per precondition | Loan Summary | Loan Number from precondition | The system loads the loan in editable state | NA

Generate steps to validate all acceptance criteria.

Expected Results must start with "The system".

Requirement Mapping:
Business validation steps must map to {user_story_id}_AC_XX.

Final Step:
Step XX | Log out from H2O-A | Application Header | NA | The system terminates the session and redirects to the login page | NA

---
