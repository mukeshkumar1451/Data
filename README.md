===== RETRIEVAL DEBUG LOG =====

----- QUERY SENT TO VECTOR DB -----

User Story: Modernized Audit additions - DIS > Generate Disclosures Fields
Description: Business would like to add the following fields to Modernized Audit. 
 
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
Acceptance Criteria: Description
Navigate to H2O UI.
Verify that the "Description" field is rendered as a Text.
Verify that the field is not privilege restricted.

HPML
Navigate to DIS > Generate Disclosures > Generate Disclosure.
Verify that the "HPML" field is rendered as a Dropdown.
Verify that the "HPML" dropdown contains exactly the following options:
    - Yes
    - No
Verify that the field is not privilege restricted.

Intent to Proceed
Navigate to DIS > Generate Disclosures.
Verify that the "Intent to Proceed" field is rendered as a Checkbox.
Verify that the "Intent to Proceed" checkbox can be checked and unchecked.
Verify that the field is not privilege restricted.

Mortgage Broker Fee Agreement
Navigate to DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement.
Verify that the "Mortgage Broker Fee Agreement" field is rendered as a Dropdown.
Verify that the "Mortgage Broker Fee Agreement" dropdown contains exactly the following options:
    - Yes
    - No
Verify that the field is restricted based on user privilege.

Mortgage Broker License Type
Navigate to DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement.
Verify that the "Mortgage Broker License Type" field is rendered as a Dropdown.
Verify that the "Mortgage Broker License Type" dropdown contains exactly the following options:
    - CFL
    - DRE
    - RML
Verify that the field is restricted based on user privilege.



====================================
CHANNEL: RTL
====================================

Retrieved 20 documents


--- RAW DOC 1 ---
TestCaseId: 586422_585804_RTL_06
Content Preview:

TestCase: 586422_585804_RTL_06
Channels: RTL

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.

1. Channel: RTL
2. Loan Type: Conventional
3. Document type: Full
4. Loan Stage: Application Accepted.
5. Product: Any ARM Product. 
Example: CL5 or NRSEF30
6. Early Disclosures should be generated and sent with any ARM Product.
7. Loan should be after Feb 2024 release deployment.


=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT1 Environment
Screen: Dashboard
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 2
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 3
Description: Navigate to DIS> Generate Disclosure screen
Screen: Generate Disclosure
Test Data: nan
Expected Result: Generate Disclosure screen should be displayed.

Step 4
Description: Click on the 'View All COC Fields' button
Screen: Generate Docs
Test Data: nan
Expected Result: 1. 'TRID Product Type' should be present in the Loan Attribute section of the grid.
2. Sent Disclosure, Last Disclosure and Loan columns should display 'ARM' as the product assigned was a ARM Product.
3. Lock column should display 'NA' as Lock column will not be applicable for this attribute.

Step 5
Description: Click on Close button.
Screen: Generate Docs
Test Data: nan
Expected Result: Change Of Circumstance field comparison Modal should be closed.

Step 6
Description: Stage the Loan to Closing Disclosures Ordered and Navigate to DOCS> Generate Docs 
Screen: Generate Docs
Test Data: nan
Expected Result: Generate Docs screen should be displayed.

Step 7
Description: Generate and send CD - Initial Borrower Package.
Note:
CD - Settlement Agent Approval should be generated and posted.
Credit, AUS and Fee Quote and should be completed as per the process prior this step execution.
Screen: Generate Docs
Test Data: nan
Expected Result

--- RAW DOC 2 ---
TestCaseId: 564936_564981_RTL_01
Content Preview:

TestCase: 564936_564981_RTL_01
Channels: RTL

Pre-Condition & Assumptions:
Create a new loan in Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: "Application Accepted"
6. No. of Application: 2
7. No. of Borrowers in 1st Application: 2
8. No. of Borrowers in 2nd Application:1
For eg: 1st application has Andy 
America and Amy America, and 2nd Application has Alice Firstimer.

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 environment.
Screen: https://qch2o.caliberdirect.com
Test Data: nan
Expected Result: Login should be successful.

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened.

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Audit
Test Data: nan
Expected Result: "Search" button should not be present in "Modernized 
Audit" screen.

Step 04
Description: Verify default selection value of "View" dropdown.
Screen: Audit
Test Data: nan
Expected Result: "Select" should be selected as default in "View" 
dropdown.

Step 05
Description: Click on "View" dropdown.
Screen: Audit
Test Data: nan
Expected Result: User should verify that "(P)" is displayed before primary borrower of 1st application and "View" dropdown should display following options in the order:
• Select
• Loan
• (P) Name of Primary Borrower of 1st Application
• Name of Co-Borrower of 1st Application
• Name of Borrower of 2nd Application.

Step 06
Description: Select "Loan" from "View" dropdown.
Screen: Audit
Test Data: nan
Expected Result: User should be able to select "Loan" from "View" 
dropdown.

Step 07
Description: Verify audit logs.
Screen: Audit
Test Data: nan
Expected Result: Audit logs should not be displayed until user clicks on 
"Search" field.

Step 08
Description: Click on "Search" field.
Screen: Audit
Test Data: nan
Expected Result: "

--- RAW DOC 3 ---
TestCaseId: 718524_RTL_01
Content Preview:

TestCase: 718524_RTL_01
Channels: RTL

Pre-Condition & Assumptions:
Create Loan from Customer Portal with below pre-conditions.
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product: CF30.
5. Loan stage: Application Accepted.
6.  DIS > LE Fees > Rate Information-
"Borrower will receive a __  %  /  $  
$0.00 __ Lender Credit for the interest rate of __ % " radio button should be selected.

=========== TEST STEPS ===========

Step 01
Description: Log in to UAT1 H2O-A
Screen: Login page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 02
Description: Open a Loan which is created as per the preconditions.
Screen: Dashboard
Test Data: nan
Expected Result: Loan should be opened and Loan summary page should be opened.

Step 03
Description: Navigate to Audit and Search field YSPPointsAtNoLock in search box
Screen: Audit
Test Data: nan
Expected Result: YSPPointsAtNoLock should be present as Field name along with User, Time Occurred, Previous Value, New Value and New value should match the percent value entered in Rate information.

Step 04
Description: Navigate to DIS > LE Fees screen and change the rate information value and click on master Save.
Screen: nan
Test Data: nan
Expected Result: Rate information value should be changed and saved.

Step 05
Description: Navigate to Audit and Search field YSPPointsAtNoLock in search box
Screen: Audit
Test Data: nan
Expected Result: YSPPointsAtNoLock should be present as Field name along with User, Time Occurred, Previous Value, New Value and New value should match the percent value entered in Rate information.

Step 06
Description: Search field YSPPointsAtNoLock in search box ApplicationTakenByMediumID. 
Screen: Audit
Test Data: nan
Expected Result: ApplicationTakenByMediumID should be present as Field name along with User, Time Occurred, Previous Value, New Value and Previous Value should be blank and New Value should be 2 by default.

Step 07
Description: Navigate t

--- RAW DOC 4 ---
TestCaseId: 680177_679185_RTL_03
Content Preview:

TestCase: 680177_679185_RTL_03
Channels: RTL

Pre-Condition & Assumptions:
Originate the loan from H2O-A
1. Channel: RTL 
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: "Application Accepted"
6. User should Not have privilege 1301
7. Subject Property Address: 3369 Saint Helena Hwy N, Saint Helena, CA, 94574 (Test Data that gives DU Title Waiver as None)
8. Loan creation date should be such that it fllls within the waiver functionality roll out date.


=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment 
Screen: Login Page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 02
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 03
Description: Navigate to AUS > Request tab and Submit AUS request for DU.
Screen: AUS
Test Data: nan
Expected Result: DU should be run successfully.

Step 04
Description: Navigate to Selection tab and verify the Title Waiver > Waiver Eligibility
Screen: AUS
Test Data: nan
Expected Result: Title Waiver > Waiver Eligibility should be "None"

Step 05
Description: Navigate to 1008 > UW Analysis > AU/DU Findings section
Screen: UW Analysis
Test Data: nan
Expected Result: Title Waiver' field should be present with value as 'None' and with Lock Icon next to it with both fields Disabled and Read-Only.

Step 06
Description: Logout and Login again with Privilege 1301 and Navigate to 1008 > UW Analysis > AU/DU Findings section
Screen: UW Analysis
Test Data: nan
Expected Result: Title Waiver' field should be present with value as 'None' and with Lock Icon next to it.

Step 07
Description: Verify 'Title Waiver' field drop down is enabled and user is able to modify the value from 'None' to 'Eligible'
Screen: UW Analysis
Test Data: nan
Expected Result: Title Waiver' should be changed to 'Eligible' from 

--- RAW DOC 5 ---
TestCaseId: 586422_585804_RTL_01
Content Preview:

TestCase: 586422_585804_RTL_01
Channels: RTL

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.

1. Channel: RTL
2. Loan Type: Conventional
3. Document type: Full
4. Loan Stage: Application Accepted.
5. Product: CF30
6. Early Disclosures should be generated and sent with CF30 Product.
7. Loan should be after Feb 2024 release deployment.

=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT1 Environment
Screen: Dashboard
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 2
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 3
Description: Navigate to DIS> Generate Disclosure screen
Screen: Generate Disclosure
Test Data: nan
Expected Result: Generate Disclosure screen should be displayed.

Step 4
Description: Click on the 'View All COC Fields' button
Screen: Generate Docs
Test Data: nan
Expected Result: 1. 'TRID Product Type' should be present in the Loan Attribute section of the grid.
2. Sent Disclosure, Last Disclosure and Loan columns should display 'Fixed' as the product assigned was CF30 which is a fixed product.
3. Lock column should display 'NA' as Lock column will not be applicable for this attribute.

Step 5
Description: Click on Close button.
Screen: Generate Docs
Test Data: nan
Expected Result: Change Of Circumstance field comparison Modal should be closed.

Step 6
Description: Stage the Loan to UW Submitted and complete below tasks for Auto CD Order
• Loan is locked
• Loan is approved 
• All Prior to CD conditions have been cleared (Data Validations will run to ensure these have been met) 
• The Estimated Closing Date is within 14 calendar days
Screen: Loan Summary
Test Data: nan
Expected Result: Loan stage should be UW Submitted and sub status should be Closing Disclosure Ordered.

Step 7
Description: Navigate to DOCS> Generate Docs 
Screen: Generate Docs


--- RAW DOC 6 ---
TestCaseId: 579217_RTL_01
Content Preview:

TestCase: 579217_RTL_01
Channels: RTL

Pre-Condition & Assumptions:
Create a new loan using Mismo 3.4 XML file
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product: CF30
5. Stage: Application Accepted
6. No. of Borrower: 4(2 Applications)
7. Fee Quote ran on loan.





=========== TEST STEPS ===========

Step 1
Description: Login to H2O Application in UAT1 environment 

Screen: H2OA login page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.


Step 2
Description: Open the loan which is created as per the Pre-Condition.  
Screen: H2O-A Dashboard
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to "1008" --> "UW Analysis>Qualifying Details>Qualifying Ratio" and verify that value is present in "Top(Total Prim Exp/Inc)" field.

Screen: UW Analysis
Test Data: nan
Expected Result: Value should be noted.

Step 4
Description: Under "Qualifying Ratio" section and verify that value is present in "Bottom(Total Payment/Inc)"field.

Screen: UW Analysis
Test Data: nan
Expected Result: Value should be noted.

Step 5
Description: Under "Calculations" section and verify that value is present for  "Hazard Insurance "field.

Screen: UW Analysis
Test Data: nan
Expected Result: Value should be noted.

Step 6
Description: Under "Calculations" section and verify that value is present for  "Property Taxes" field.

Screen: UW Analysis
Test Data: nan
Expected Result: Value should be noted.

Step 7
Description: Stage the Loan to In Processing
Screen: Loan Summary
Test Data: nan
Expected Result: Loan should be In Processing stage.

Step 8
Description: Navigate to Tools --> Modernized Audit and click on Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in a new window

Step 9
Description:  Verify the option "Loan" defaulted to "View" field. 
Screen: Modernized Audit
Test Data: nan
Expected Result: "Lo

--- RAW DOC 7 ---
TestCaseId: 586422_585804_RTL_05
Content Preview:

TestCase: 586422_585804_RTL_05
Channels: RTL

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.

1. Channel: RTL
2. Loan Type: Conventional
3. Document type: Full
4. Loan Stage: Application Accepted.
5. Product: CF30
6. Early Disclosures should be generated and sent with CF30 Product.
7. Loan should be after Feb 2024 release deployment.


=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT1 Environment
Screen: Dashboard
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 2
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 3
Description: Stage the loan to UW Submitted and Manual order CD.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan stage should be UW Submitted and sub status should be Closing Disclosure Ordered.

Step 4
Description: Navigate to DOCS> Generate Docs 
Screen: Generate Docs
Test Data: nan
Expected Result: Generate Docs screen should be displayed.

Step 5
Description: Generate and send CD - Initial Borrower Package Via USPS.
Note:
CD - Settlement Agent Approval should be generated and posted Prior to this step.

Screen: Generate Docs
Test Data: nan
Expected Result: CD - Initial Borrower Package should be generated and sent.

Step 6
Description: Navigate to Docs > CD Fees Screen and Increase any Fee.
Screen: CD Fees Screen
Test Data: nan
Expected Result: Fee should be Increased.

Step 7
Description: Wait till Nightly batch process to be completed
OR
Run the task Master Job.
Note: This process will happen everyday 2pm IST
Screen: nan
Test Data: nan
Expected Result: Nightly batch process or Task Master Job should be completed.

Step 8
Description: Navigate to Left panel > File Flow tab and verify the tasks.
Screen:  File Flow tab
Test Data: nan
Expected Result: CDP – Change Circumstance - CDP should be triggered for the buydown change.

Step 9

--- RAW DOC 8 ---
TestCaseId: 579578_RTL_01
Content Preview:

TestCase: 579578_RTL_01
Channels: RTL

Pre-Condition & Assumptions:
Create a new loan using Mismo 3.4 XML file
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product: CF30
5. Stage: Created
6. No. of Borrower: 1





=========== TEST STEPS ===========

Step 1
Description: Login to H2O Application in UAT1 environment 

Screen: H2OA login page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.


Step 2
Description: Open the loan which is created as per the Pre-Condition. 
Screen: H2O-A Dashboard
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to "1003" --> "Loan Summary" screen and verify that values are available in "P&I:" and "Housing Exp." field under Calculations section.
Screen: Loan Summary
Test Data: nan
Expected Result: Value should be available for both the fields.

Step 4
Description: Navigate to Tools --> Modernized Audit and click on Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in a new window

Step 5
Description: Verify the option "Loan" defaulted to "View" field. 
Screen: Modernized Audit
Test Data: nan
Expected Result: "Loan" should be defaulted for "View" field.

Step 6
Description: Select "Proposed Housing Expense Total (Calc)" and "Proposed Housing - First Mortgage (P&I) (Calc)" for "Field" and click on Search.
Screen: Modernized Audit
Test Data: nan
Expected Result: Only "Proposed Housing Expense Total (Calc)" and "Proposed Housing - First Mortgage (P&I) (Calc)" should not be displayed in the result grid


Step 7
Description: Stage the loan to Application Accepted stage
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Stage should be Application Accepted

Step 8
Description: Navigate to Tools --> Modernized Audit and click on Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in a 

--- RAW DOC 9 ---
TestCaseId: 647338_647343_RTL_01
Content Preview:

TestCase: 647338_647343_RTL_01
Channels: RTL

Pre-Condition & Assumptions:
Create a Loan from Customer Portal:
1. Channel: RTL
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: "UW Submitted"
6. H2O-D Underwriting Flag should be "N".
7. User should have privilege 1016.

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 Environment.
Screen: Login Page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Log in should be successful.

Step 02
Description: Open the Loan created as per preconditions
Screen: Loan Summary
Test Data: Loan number:
Expected Result: Loan summary screen should be opened

Step 03
Description: Navigate to Screens-> Government Insuring > Insurance Tracking screen and verify the fields.
Screen:  Screens-> Government Insuring > Insurance Tracking
Test Data: nan
Expected Result: Below fields should be present:
• ‘Government insuring received date’ label is renamed to ‘Government Audit Received Date’.
• A new 'date picker' field with label ‘Post-Closing Reviewed Date’ should be present under ‘Agency Case Number’ field.
• ‘Government insuring review date’ label is renamed to ‘Government Insuring Review Date’.

Step 04
Description: Verify the Below fields:
• Government Audit Received Date
• Post-Closing Reviewed Date
• Government Insuring Review Date
Screen:  Screens-> Government Insuring > Insurance Tracking
Test Data: nan
Expected Result: All the fields should be disabled.

Step 05
Description: Assign privilege 1016 to the IDUW user
Screen: nan
Test Data: nan
Expected Result: Privilege should be assigned.

Step 06
Description: Navigate to Screens-> Government Insuring > Insurance Tracking screen and Verify the Below fields:
• Government Audit Received Date
• Post-Closing Reviewed Date
• Government Insuring Review Date
Screen:  Screens-> Government Insuring > Insurance Tracking
Test Data: nan
Expected Result: All the fields should be enabled.

Step 07
Description: Click on 

--- RAW DOC 10 ---
TestCaseId: 680177_679185_RTL_02
Content Preview:

TestCase: 680177_679185_RTL_02
Channels: RTL

Pre-Condition & Assumptions:
Originate the loan from H2O-A
1. Channel: RTL 
2. Loan Purpose: Refinance
3. Loan Type: VA
4. Product Code: VF30
5. Loan Stage: "Application Accepted"
6. SSP: Mortgage Connect, LP
7. LPA should be run.
8. User should have Privilege 1301.
9. Subject Property Address: 3431 Fort Ave, Eureka, CA, 95503 (Test Data that gives DU Title Waiver as Eligible)
10. Loan creation date should be such that it fllls within the waiver functionality roll out date.



=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment 
Screen: Login Page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 02
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 03
Description: Navigate to 1008 > UW Analysis > AU/DU Findings section
Screen: UW Analysis
Test Data: nan
Expected Result: Title Waiver' field should be present with value as 'Eligible' and with Lock Icon next to it but the field and Lock should be disabled.

Step 04
Description: Logout and Login without Privilege 1301 and Navigate to AUS > Request tab and Submit AUS request for both DU and LPA (Multi- AUS)
Screen: AUS
Test Data: nan
Expected Result: AUS should be run and LPA should be should be selected as Recommendation.

Step 05
Description: Navigate to 1008 > UW Analysis > AU/DU Findings section
Screen: UW Analysis
Test Data: nan
Expected Result: Title Waiver' field should be present with value as 'None' and with Lock Icon next to it but the field and Lock should be disabled.

Step 06
Description: Navigate to AUS > Request tab and Select DU as Recommendation.
Screen: AUS
Test Data: nan
Expected Result: DU should be should be selected as Recommendation.

Step 07
Description: Logout and Login with Privilege 1301 and Navigate to 1008 > UW Analysis > AU/DU Findin

--- RAW DOC 11 ---
TestCaseId: 573135_RTL_02
Content Preview:

TestCase: 573135_RTL_02
Channels: RTL

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: "Created"
6. No. of Application: 2
7. No. of Borrowers in 1st Application: 2
8. No. of Borrowers in 2nd Application:2
For e.g.: 1st application has Andy 
America and Amy America, and 2nd Application has Alice Firstimer and Ken Customer




=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful 

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in the new window

Step 04
Description: Verify the Fields in new Modernized Audit Screen
Screen: Modernized Audit
Test Data: nan
Expected Result: The following Fields should be available in the new Modernized Audit screen:
1. Search Frame under Audit 
2. View dropdown with default selection as "Loan" 
3. Search text field labeled as Field 
4. "Date Occurred, From" Field 
5. "Date Occurred, To" Field
6. Search Button
7. Reset Filters Button

Step 05
Description: Click on "View" dropdown.
Screen: Modernized Audit
Test Data: nan
Expected Result: "(P)" should be displayed before primary borrower of 1st application and "View" dropdown should display following options in the order:
• Select
• Loan
• (P) Name of Primary Borrower of 1st Application
• Name of Co-Borrower of 1st Application
• Name of Borrower of 2nd Application.
• Name of Co-borrower of 2nd application.

Step 06
Description: Select "Loan" from "View" dropdown.
Screen: Modernized Audit
Test Data: nan
Expected Result: "Loan" from "View" dro

--- RAW DOC 12 ---
TestCaseId: 718523_RTL_01
Content Preview:

TestCase: 718523_RTL_01
Channels: RTL

Pre-Condition & Assumptions:
Create a loan from Customer Portal:
1. Channel: RTL
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product Code: Any
5. Loan stage should be UW Submitted
6. Early Disclosure should be generated, sent via eSign ,Esign should be completed and disclosure should be received. 


=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment
Screen: Login Page
Test Data: https://uath2o.newrez.com
Expected Result: Login should be successful

Step 2
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to DOCS --> Closing Details and check the below dates:
• Earliest Consummation Date
• Most Recent LE Receipt Date
• Rescission Date
• Agent Disbursement Date
Note: Enter some date in Agent Disbursement date if date is not present.
Screen: DOCS --> Closing Details
Test Data: nan
Expected Result: Mentioned dates should be present under DOCS --> Closing Details.

Step 4
Description: Navigate to Tools --> Modernized Audit and verify the below field values:
• Earliest Consummation Date
• Most Recent LE Receipt Date
• Rescission Date
• Agent Disbursement Date
Screen: Modernized Audit
Test Data: nan
Expected Result: Previous values should be displayed as blank and New values should be displayed as per step 3

Step 5
Description: Navigate to DIS --> Generate Disclosure and change the "Receipt Date:" under Transaction History.
Screen: DIS --> Generate Disclosure
Test Data: nan
Expected Result: "Receipt Date" should be changed.

Step 6
Description: Navigate to DOCS --> Closing Details and verify the "Most Recent LE Receipt Date" is same as "Receipt Date". Again change the Agent Disbursement Date and Recession date by entering a different date.
Screen: DOCS --> Closing Details
Test Data: nan
Expected Result: Dates should be updated

--- RAW DOC 13 ---
TestCaseId: 561836_RTL_01
Content Preview:

TestCase: 561836_RTL_01
Channels: RTL

Pre-Condition & Assumptions:
Create a new loan using Mismo 3.4 XML file
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product: CF30
5. Stage: Application Accepted
6. No. of Borrower: 1





=========== TEST STEPS ===========

Step 01
Description: Login to H2O Application in UAT1 environment 

Screen: H2OA login page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.


Step 02
Description: Open the loan which is created as per the Pre-Condition.  
Screen: H2O-A Dashboard
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 03
Description: Navigate to "1003" --> "Loan Summary" screen and verify that some amount is present in "Total Loan Amount" and "Income" field.
Screen: Loan Summary
Test Data: nan
Expected Result: Value should be available for both the fields.

Step 04
Description: Navigate to Tools --> Modernized Audit and click on Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in a new window

Step 05
Description:  Verify the option "Loan" defaulted to "View" field. 
Screen: Modernized Audit
Test Data: nan
Expected Result: "Loan" should be defaulted for "View" field.

Step 06
Description: Verify the Audit logs
Screen: Modernized Audit
Test Data: nan
Expected Result: 1. "Total Loan Amt (Calc)" should be displayed in the result grid along with other fields.
2. "Previous Value" should be NULL and "New Value" should be current value for the "Field" value "Total Loan Amt (Calc)".


Step 07
Description: Input the below detail in Audit screen:
• Type "Total" and select "Total Loan Amt (Calc)" for "Field" and click on search.
Screen: Modernized Audit
Test Data: nan
Expected Result: 1. Only "Total Loan Amt (Calc)" should be displayed in the result grid.
2. "Previous Value" should be NULL and "New Value" should be current value for the "Field" value "Total Loan Amt (Cal

--- RAW DOC 14 ---
TestCaseId: 623485_623489_RTL_01
Content Preview:

TestCase: 623485_623489_RTL_01
Channels: RTL

Pre-Condition & Assumptions:
1. Channel: Retail.
2. Loan Purpose: Purchase.
3. Loan Type: Conventional..
4. Product: CF30.
5. Loan Stage: "Application Accepted".

User should have privilege 1280.
Loan should be created after Nov release deployment.

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT environment.
Screen: Dashboard
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened.

Step 03
Description: Navigate to Screens > Pre-Underwriting > Pre-Underwriting main > Expiration Dates and verify the fields.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Below new fields should be displayed under Expiration Dates section.
• Income Document Date
• Assets Document Date

Step 04
Description: Click on the text box for "Income Document Date" and "Asset Document Date" field.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Calendar should be opened.

Step 05
Description: Select the dates for each field and click on save.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Dates should be selected for each field.

Step 06
Description: Navigate to Tools > Modernized Audit and verify the 'Income Document Expiration' & 'Assets Document Date' record
Screen: Modernized Audit
Test Data: nan
Expected Result: Records should be display pervious value and new value correctly.

Step 07
Description: Close the Mod Audit and Navigate to Tools >  Audit and verify the 'Income Document Expiration'& 'Assets Document Date' record
Screen: Audit pop up
Test Data: nan
Expected Result: Records should be display pervious value and new value correctly.

Step 08
Description: Navigate to 1008 > UW analysis > Expiration Dates and verify below fields.
• Income Document Date
• Assets Docume

--- RAW DOC 15 ---
TestCaseId: 573135_RTL_03
Content Preview:

TestCase: 573135_RTL_03
Channels: RTL

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: VA
4. Document type: Full
5. Loan Stage: "Application Accepted" 
6. Product: VF30

Note: Changes has been made in the loan to display minimum 60 Audit Logs



=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful 

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in the new window

Step 04
Description: Verify the Fields in new Modernized Audit Screen
Screen: Modernized Audit
Test Data: nan
Expected Result: The following Fields should be available in the new Modernized Audit screen:
1. Search Frame under Audit 
2. View dropdown with default selection as "Loan" 
3. Search text field labeled as Field 
4. "Date Occurred, From" Field 
5. "Date Occurred, To" Field
6. Search Button
7. Reset Filters Button

Step 05
Description: Verify the Audit Logs
Screen: Modernized Audit
Test Data: nan
Expected Result: 50 loan level audited entries should be displayed in Audit logs by default

Note: For loan having less than 50 loan level audit records, only those number of records should be displayed.  

Step 06
Description: Verify Records per page dropdown at footer 
Screen: Modernized Audit
Test Data: nan
Expected Result: Records per page dropdown at footer should be defaulted with value of 50  

Step 07
Description: Change per page dropdown selection for Records per page and verify dropdown changes 
Screen: Modernized Audit
Test Data: nan
Expected Result: Screen size should load records based on the 

--- RAW DOC 16 ---
TestCaseId: 573135_RTL_01
Content Preview:

TestCase: 573135_RTL_01
Channels: RTL

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Document type: Full
5. Loan Stage: "Application Accepted" 
6. Product: CF30





=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful 

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in the new window

Step 04
Description: Verify the Fields in new Modernized Audit Screen
Screen: Modernized Audit
Test Data: nan
Expected Result: The following Fields should be available in the new Modernized Audit screen:
1. Search Frame under Audit 
2. View dropdown with default selection as "Loan" 
3. Search text field labeled as Field 
4. "Date Occurred, From" Field 
5. "Date Occurred, To" Field
6. Search Button
7. Reset Filters Button

Step 05
Description: Click on calendar icon next to "Date Occurred, From"
Screen: Modernized Audit
Test Data: nan
Expected Result: Calendar control should be opened

Step 06
Description: Select a date on the Calendar 

Screen: Modernized Audit
Test Data: nan
Expected Result: Date selected in Calendar should be loaded in "Date Occurred, From" field

Step 07
Description: Click on Search Button
Screen: Modernized Audit
Test Data: nan
Expected Result:  "Date Occurred, To can not be empty while Date Occurred, From is not. Please select both the dates or remove both" UI message should be displayed

Step 08
Description: Click on Ok
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be displayed

Step 09
Description: Re

--- RAW DOC 17 ---
TestCaseId: 567672_RTL_01
Content Preview:

TestCase: 567672_RTL_01
Channels: RTL

Pre-Condition & Assumptions:
Pre-Conditions:
1. Channel: RTL
2. Loan should have the below Audit entries starting from 7/14/2022 to 7/15/2023(within the given dates)
>Property Type
>Appraisal Type
>Underwriting Method
>Impounds Type

Reference Screens:
>Property Type : 1008>Appraisal & Property>Property Information>Type of Property.
>Appraisal Type : 1008>Appraisal & Property>Appraisal Type
>Underwriting Method : 1008>UW Analysis>AU/Audit Findings>Radio button or checkbox selection 
>Impounds Type : 1003>Loan Summary>Loan Details>Impounds

=========== TEST STEPS ===========

Step 1
Description: Log in to UAT1 H2O-A
Screen: https://qch2o.caliberdirect.com
Test Data: nan
Expected Result: Login should be successful

Step 2
Description: Open a Loan which is created or existing as per the preconditions  
Screen: Dashboard
Test Data: nan
Expected Result: Loan should be opened and Loan summary page should be opened.

Step 3
Description: Hover over 'Tools' and verify the new Item 'Modernized Audit' is displayed.
Screen: Loan Header
Test Data: nan
Expected Result: The New Item 'Modernized Audit' should be displayed to the user when user hover over 'Tools'

Step 4
Description: Click on the new Item 'Modernized Audit'.
Screen: Modernized Audit window
Test Data: nan
Expected Result: Verify the system opens a New Window.

Step 5
Description: Type 'Property Type' in Search field and click on Search Button
Screen: Modernized Audit window
Test Data: nan
Expected Result: Audit records of Property Type should be displayed

Step 6
Description: Note down the Previous value and New value.
Screen: Modernized Audit window
Test Data: nan
Expected Result: Previous value and New value should be noted down.

Step 7
Description: Type Appraisal Type in Search field and click on Search Button.
Screen: Modernized Audit window
Test Data: nan
Expected Result: Audit records of Appraisal Type should be displayed

Step 8
Description: Note down the Previous value

--- RAW DOC 18 ---
TestCaseId: 565797_RTL_01
Content Preview:

TestCase: 565797_RTL_01
Channels: RTL

Pre-Condition & Assumptions:
Create two new loans in Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: "Application Accepted"

Note: Testing should be done in Chrome browser

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 environment.
Screen: https://qch2o.caliberdirect.com
Test Data: nan
Expected Result: Login should be successful.

Step 02
Description: Open the first loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened fort the first loan.

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Audit
Test Data: nan
Expected Result: User should be navigated to "Modernized 
Audit" screen.

Step 04
Description: Don't close the "Modernized Audit" screen and navigate back to "Loan Summary" screen of the first loan.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened for the first loan.

Step 05
Description: Enter the second loan number in the 
"Search" box and click on "Go" button.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened for the second loan.

Step 06
Description: Navigate back to unclosed "Modernized 
Audit" screen of the first loan.
Screen: Audit
Test Data: nan
Expected Result: System should display a warning pop up with verbiage "Loan has changed. Click 'Ok' to refresh." and the pop up should also display "Ok" button.

Step 07
Description: Click "Ok" button on the pop up.
Screen: Audit
Test Data: nan
Expected Result: User should be able to verify the following:
• System should sync "Modernized Audit" 
screen to current loan (second loan).
• "Modernized Audit" screen should reset to its default state.
• "Select" should be selected as default value in "View" dropdown.
• Loan number field should reflect the current (second) l

--- RAW DOC 19 ---
TestCaseId: 659272_659276_RTL_03
Content Preview:

TestCase: 659272_659276_RTL_03
Channels: RTL

Pre-Condition & Assumptions:
1. Channel: Retail
2. Loan Purpose: Purchase
3. Loan Type: USDA
4. Product: USDAF30
5. AUS successfully run
6. Loan Stage: "Application Submitted"

User should have privilege 1280
Loan should be created after Jan release deployment.

=========== TEST STEPS ===========

Step 01
Description: Login to H2O in UAT environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com/
Expected Result: Login should be successful

Step 02
Description: Open the loan as per pre-condition
Screen: nan
Test Data: nan
Expected Result: Loan summary screen should be displayed.

Step 03
Description: Navigate to 1008 --> UW analysis --> Expiration Dates and verify the "Credit Report Order" date and "Credit Report Expiration" field.
Screen: 1008 > UW analysis
Test Data: nan
Expected Result: • "Credit Report Order" date should be displayed.
• Credit Report Expiration" date should be auto populate as 'Credit Report Order Date + 120 days' and Credit Report Expiration checkbox should be unchecked

Step 04
Description: Navigate to Screen --> Pre-Underwriting --> Pre-Underwriting Main --> Expiration Dates and verify the "Credit Report Expiration" field.
Screen: Pre-Underwriting Main
Test Data: nan
Expected Result: Credit Report Expiration" date should be auto populate as 'Credit Report Order Date + 120 days' and Credit Report Expiration checkbox should be unchecked

Step 05
Description: Navigate to Tools > Modernized Audit and verify the 'Credit Report Expiration'
Screen: Modernized Audit
Test Data: nan
Expected Result: • Record of "Credit Report Expiration", previous value should be blank and New value should be displayed as per calculation(Step 4).
• Previous value should be "Blank" and New value should be "False" for  "Credit Report Expiration Not Required" 

Step 06
Description: Close the Mod Audit and Navigate to Tools >  Audit and verify the ''Credit Report Expiration'& 'Credit Report Order Date' record
Screen: T

--- RAW DOC 20 ---
TestCaseId: 623485_623489_RTL_02
Content Preview:

TestCase: 623485_623489_RTL_02
Channels: RTL

Pre-Condition & Assumptions:
1. Channel: Retail.
2. Loan Purpose: Purchase.
3. Loan Type: Conventional..
4. Product: CF30.
5. Loan Stage: "Application Accepted".

User should not have privilege 1280.
Loan should be created after Nov release deployment..

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT environment.
Screen: Dashboard
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened.

Step 03
Description: Navigate to Screens > Pre-Underwriting > Pre-Underwriting main > Expiration Dates and verify the fields.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Below new fields should be displayed and it should be disable should not be able to modify/update.
• Income Document Date
• Assets Document Date

Step 04
Description: Navigate to 1008 > UW analysis > Expiration Dates and verify below fields.
• Income Document Date
• Assets Document Date
Screen: UW analysis
Test Data: nan
Expected Result: New fields should be displayed and it should be disable should not be able to modify/update.

Step 05
Description: Logout from H2O A.
Screen: Dashboard
Test Data: nan
Expected Result: Log out should be successful.


--- RERANKED ORDER ---
Rank 1: 564936_564981_RTL_01
Rank 2: 579217_RTL_01
Rank 3: 579578_RTL_01
Rank 4: 573135_RTL_02
Rank 5: 561836_RTL_01
Rank 6: 573135_RTL_01
Rank 7: 565797_RTL_01

--- STRUCTURED EXTRACTION ---
{
  "scenario": "564936_564981_RTL_01",
  "script": "Channels: RTL",
  "precondition": "Create a new loan in Mismo 3.4 XML file. Channel: RTL, Loan Purpose: Purchase, Loan Type: Conventional, Product Code: CF30, Loan Stage: 'Application Accepted', No. of Application: 2, No. of Borrowers in 1st Application: 2, No. of Borrowers in 2nd Application: 1. For example: 1st application has Andy America and Amy America, and 2nd Application has Alice Firstimer.",
  "steps": [
    {
      "stepNo": "01",
      "description": "Log in to H2O-A in UAT1 environment.",
      "expectedResult": "Login should be successful."
    },
    {
      "stepNo": "02",
      "description": "Open the loan which is created as per the Pre-Conditions.",
      "expectedResult": "Loan summary screen should be opened."
    },
    {
      "stepNo": "03",
      "description": "Navigate to Tools->Modernized Audit.",
      "expectedResult": "'Search' button should not be present in 'Modernized Audit' screen."
    },
    {
      "stepNo": "04",
      "description": "Verify default selection value of 'View' dropdown.",
      "expectedResult": "'Select' should be selected as default in 'View' dropdown."
    },
    {
      "stepNo": "05",
      "description": "Click on 'View' dropdown.",
      "expectedResult": "User should verify that '(P)' is displayed before primary borrower of 1st application and 'View' dropdown should display following options in the order: \u2022 Select \u2022 Loan \u2022 (P) Name of Primary Borrower of 1st Application \u2022 Name of Co-Borrower of 1st Application \u2022 Name of Borrower of 2nd Application."
    },
    {
      "stepNo": "06",
      "description": "Select 'Loan' from 'View' dropdown.",
      "expectedResult": "User should be able to select 'Loan' from 'View' dropdown."
    },
    {
      "stepNo": "07",
      "description": "Verify audit logs.",
      "expectedResult": "Audit logs should not be displayed until user clicks on 'Search' field."
    },
    {
      "stepNo": "08",
      "description": "Click on 'Search' field.",
      "expectedResult": "'Search' field should display all loan level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "09",
      "description": "Verify all loan level audit field logs.",
      "expectedResult": "User should be able to verify all loan level audit field logs."
    },
    {
      "stepNo": "10",
      "description": "Enter 'ap' in the search field.",
      "expectedResult": "System should not filter audit fields list matching the characters 'ap'."
    },
    {
      "stepNo": "11",
      "description": "Enter 'appr' in the search field.",
      "expectedResult": "System should filter audit fields list matching the characters 'appr'."
    },
    {
      "stepNo": "12",
      "description": "Enter 'Type of Property' in the search field and select 'Type of Property'.",
      "expectedResult": "\u2022 User should verify that space is also counted as a character. \u2022 System should populate all the records of the selected field 'Type of Property'."
    },
    {
      "stepNo": "13",
      "description": "Select primary borrower of 1st application from 'View' dropdown.",
      "expectedResult": "User should be able to select primary borrower of 1st application from 'View' dropdown."
    },
    {
      "stepNo": "14",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all primary borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "15",
      "description": "Select co-borrower of 1st application from 'View' dropdown.",
      "expectedResult": "User should be able to select co-borrower of 1st application from 'View' dropdown."
    },
    {
      "stepNo": "16",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all co-borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "17",
      "description": "Select borrower of 2nd application from 'View' dropdown.",
      "expectedResult": "User should be able to select borrower of 2nd application from 'View' dropdown."
    },
    {
      "stepNo": "18",
      "description": "Verify audit logs.",
      "expectedResult": "Audit logs should not be displayed until user clicks on 'Search' field."
    },
    {
      "stepNo": "19",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "20",
      "description": "Logout from H2OA.",
      "expectedResult": "Log out should be successful."
    }
  ]
}

--- FINAL SELECTED STRUCTURED DOC ---
{
  "scenario": "564936_564981_RTL_01",
  "script": "Channels: RTL",
  "precondition": "Create a new loan in Mismo 3.4 XML file. Channel: RTL, Loan Purpose: Purchase, Loan Type: Conventional, Product Code: CF30, Loan Stage: 'Application Accepted', No. of Application: 2, No. of Borrowers in 1st Application: 2, No. of Borrowers in 2nd Application: 1. For example: 1st application has Andy America and Amy America, and 2nd Application has Alice Firstimer.",
  "steps": [
    {
      "stepNo": "01",
      "description": "Log in to H2O-A in UAT1 environment.",
      "expectedResult": "Login should be successful."
    },
    {
      "stepNo": "02",
      "description": "Open the loan which is created as per the Pre-Conditions.",
      "expectedResult": "Loan summary screen should be opened."
    },
    {
      "stepNo": "03",
      "description": "Navigate to Tools->Modernized Audit.",
      "expectedResult": "'Search' button should not be present in 'Modernized Audit' screen."
    },
    {
      "stepNo": "04",
      "description": "Verify default selection value of 'View' dropdown.",
      "expectedResult": "'Select' should be selected as default in 'View' dropdown."
    },
    {
      "stepNo": "05",
      "description": "Click on 'View' dropdown.",
      "expectedResult": "User should verify that '(P)' is displayed before primary borrower of 1st application and 'View' dropdown should display following options in the order: \u2022 Select \u2022 Loan \u2022 (P) Name of Primary Borrower of 1st Application \u2022 Name of Co-Borrower of 1st Application \u2022 Name of Borrower of 2nd Application."
    },
    {
      "stepNo": "06",
      "description": "Select 'Loan' from 'View' dropdown.",
      "expectedResult": "User should be able to select 'Loan' from 'View' dropdown."
    },
    {
      "stepNo": "07",
      "description": "Verify audit logs.",
      "expectedResult": "Audit logs should not be displayed until user clicks on 'Search' field."
    },
    {
      "stepNo": "08",
      "description": "Click on 'Search' field.",
      "expectedResult": "'Search' field should display all loan level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "09",
      "description": "Verify all loan level audit field logs.",
      "expectedResult": "User should be able to verify all loan level audit field logs."
    },
    {
      "stepNo": "10",
      "description": "Enter 'ap' in the search field.",
      "expectedResult": "System should not filter audit fields list matching the characters 'ap'."
    },
    {
      "stepNo": "11",
      "description": "Enter 'appr' in the search field.",
      "expectedResult": "System should filter audit fields list matching the characters 'appr'."
    },
    {
      "stepNo": "12",
      "description": "Enter 'Type of Property' in the search field and select 'Type of Property'.",
      "expectedResult": "\u2022 User should verify that space is also counted as a character. \u2022 System should populate all the records of the selected field 'Type of Property'."
    },
    {
      "stepNo": "13",
      "description": "Select primary borrower of 1st application from 'View' dropdown.",
      "expectedResult": "User should be able to select primary borrower of 1st application from 'View' dropdown."
    },
    {
      "stepNo": "14",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all primary borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "15",
      "description": "Select co-borrower of 1st application from 'View' dropdown.",
      "expectedResult": "User should be able to select co-borrower of 1st application from 'View' dropdown."
    },
    {
      "stepNo": "16",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all co-borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "17",
      "description": "Select borrower of 2nd application from 'View' dropdown.",
      "expectedResult": "User should be able to select borrower of 2nd application from 'View' dropdown."
    },
    {
      "stepNo": "18",
      "description": "Verify audit logs.",
      "expectedResult": "Audit logs should not be displayed until user clicks on 'Search' field."
    },
    {
      "stepNo": "19",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "20",
      "description": "Logout from H2OA.",
      "expectedResult": "Log out should be successful."
    }
  ]
}

====================================
CHANNEL: WHL
====================================

Retrieved 20 documents


--- RAW DOC 1 ---
TestCaseId: 564936_564981_WHL_01
Content Preview:

TestCase: 564936_564981_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a new loan in Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: "Created"
6. No. of Application: 2
7. No. of Borrowers in 1st Application: 2
8. No. of Borrowers in 2nd Application:2
For eg: 1st application has Andy 
America and Amy America, and 2nd Application has Alice Firstimer and Ken Customer

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 environment.
Screen: https://qch2o.caliberdirect.com
Test Data: nan
Expected Result: Login should be successful.

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened.

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Audit
Test Data: nan
Expected Result: "Search" button should not be present in "Modernized Audit" screen.

Step 04
Description: Verify default selection value of "View" dropdown.
Screen: Audit
Test Data: nan
Expected Result: "Select" should be selected as default in "View" 
dropdown.

Step 05
Description: Click on "View" dropdown.
Screen: Audit
Test Data: nan
Expected Result: User should verify that "(P)" is displayed before primary borrower of 1st application and "View" dropdown should display following options in the order:
• Select
• Loan
• (P) Name of Primary Borrower of 1st Application
• Name of Co-Borrower of 1st Application
• Name of Borrower of 2nd Application.
• Name of Co-borrower of 2nd application.

Step 06
Description: Select "Loan" from "View" dropdown.
Screen: Audit
Test Data: nan
Expected Result: User should be able to select "Loan" from "View" dropdown.

Step 07
Description: Verify audit logs.
Screen: Audit
Test Data: nan
Expected Result: Audit logs should not be displayed until user clicks on "Search" field.

Step 08
Description: Click on "Search" field.
Scree

--- RAW DOC 2 ---
TestCaseId: 738152_WHL_01
Content Preview:

TestCase: 738152_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a new loan from Blue Print with the following pre-condition:
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product: CF30 (Lender Paid)
5. Loan Stage: Created


=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment 
Screen: Login Page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 2
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to Electronic Delivery and Borrower Auth & Cert and verify the "Enable Electronic Disclosures" checkbox
Screen: Loan Summary
Test Data: nan
Expected Result: "Enable Electronic Disclosures" checkbox should be unchecked

Step 4
Description: Check the "Enable Electronic Disclosures" checkbox
Screen: Generate DOCS
Test Data: nan
Expected Result: "Enable Electronic Disclosures" checkbox should be checked

Step 5
Description: Navigate to Tools > Modernized Audit and verify the entry created for the field name "Electronic Consent":
- User who made the change 
- Time Occurred 
- Previous Value 
- New Value 
- Field name as "Electronic Consent"

Screen: Modernized Audit 
Test Data: nan
Expected Result: Record should be created for the field "Electronic Consent" and previous value should be false and new value should be true


Step 6
Description: Uncheck the "Enable Electronic Disclosures" checkbox
Screen: Loan Summary
Test Data: nan
Expected Result: "Enable Electronic Disclosures" checkbox should be unchecked

Step 7
Description: Navigate to Tools > Modernized Audit and verify the entry created for the field name "Electronic Consent":
- User who made the change 
- Time Occurred 
- Previous Value 
- New Value 
- Field name as "Electronic Consent"

Screen: Modernized Audit 
Test Data: nan
Expected Result: Record should 

--- RAW DOC 3 ---
TestCaseId: 579578_WHL_01
Content Preview:

TestCase: 579578_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a new loan using Mismo 3.4 XML file
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product: CF30
5. Stage: Created
6. Number of Borrowers: Any





=========== TEST STEPS ===========

Step 1
Description: Login to H2O Application in UAT1 environment 

Screen: H2OA login page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.


Step 2
Description: Open the loan which is created as per the Pre-Condition. 
Screen: H2O-A Dashboard
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to "1003" --> "Loan Summary" screen and verify that values are available in "P&I:" and "Housing Exp." field under Calculations section.
Screen: Loan Summary
Test Data: nan
Expected Result: Value should be available for both the fields.

Step 4
Description: Navigate to Tools --> Modernized Audit and click on Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in a new window

Step 5
Description: Verify the option "Loan" defaulted to "View" field. 
Screen: Modernized Audit
Test Data: nan
Expected Result: "Loan" should be defaulted for "View" field.

Step 6
Description: Select "Proposed Housing Expense Total (Calc)" and "Proposed Housing - First Mortgage (P&I) (Calc)" for "Field" and click on Search.
Screen: Modernized Audit
Test Data: nan
Expected Result: Only "Proposed Housing Expense Total (Calc)" and "Proposed Housing - First Mortgage (P&I) (Calc)" should not be displayed in the result grid


Step 7
Description: Stage the loan to UW Submitted stage
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Stage should be UW Submitted

Step 8
Description: Navigate to Tools --> Modernized Audit and click on Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in a new window

--- RAW DOC 4 ---
TestCaseId: 579217_WHL_01
Content Preview:

TestCase: 579217_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a new loan using Mismo 3.4 XML file
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product: CF30
5. Stage: UW Submitted
6. No. of Borrower: 1
7. Fee Quote ran on loan.




=========== TEST STEPS ===========

Step 1
Description: Login to H2O Application in UAT1 environment 

Screen: H2OA login page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.


Step 2
Description: Open the loan which is created as per the Pre-Condition.  
Screen: H2O-A Dashboard
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to "1008" --> "UW Analysis>Qualifying Details>Qualifying Ratio" and verify that value is present in "Top(Total Prim Exp/Inc)" field.

Screen: UW Analysis
Test Data: nan
Expected Result: Value should be noted.

Step 4
Description: Under "Qualifying Ratio" section and verify that value is present in "Bottom(Total Payment/Inc)"field.

Screen: UW Analysis
Test Data: nan
Expected Result: Value should be noted.

Step 5
Description: Under "Calculations" section and verify that value is present for  "Hazard Insurance "field.

Screen: UW Analysis
Test Data: nan
Expected Result: Value should be noted.

Step 6
Description: Under "Calculations" section and verify that value is present for  "Property Taxes" field.

Screen: UW Analysis
Test Data: nan
Expected Result: Value should be noted.

Step 8
Description: Navigate to Tools --> Modernized Audit and click on Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in a new window

Step 9
Description:  Verify the option "Loan" defaulted to "View" field. 
Screen: Modernized Audit
Test Data: nan
Expected Result: "Loan" should be defaulted for "View" field.

Step 10
Description: Click on Field.
Screen: Modernized Audit
Test Data: nan
Expected Result: Below fields should be availab

--- RAW DOC 5 ---
TestCaseId: 647338_647343_WHL_01
Content Preview:

TestCase: 647338_647343_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
1. The loan should be part of the new model under the H2O digital workflow and created from Broker portal
2. Loan channel: WHL
3. Ops Center: DTB Ops dallas
4. Loan Type - Conventional
5. Loan Purpose - Refinance
6. Loan Product - CF30
7. H2O-D Processing and UW Flag should be ‘Y’.                     
8. Loan Stage - "UW Submitted".
9. Loan should have IDUW activity assigned to a User which has all IDUW skill and does not have privilege 1016.

=========== TEST STEPS ===========

Step 01
Description: Login to H2O-D user dashboard as assigned IDUW activity and click on Loan number under "Initial Decision" queue
Screen: IDUW activity
Test Data: Https://uat-h2od.caliberhomeloans.com/
Expected Result: CRUW activity should be opened.

Step 02
Description: Expand "Credit" section and click on verify loan details.
Screen: IDUW activity
Test Data: Loan Number:
Expected Result: 1003-> Loan Summary screen should be opened in the middle panel.

Step 03
Description: Navigate to Screens-> Government Insuring > Insurance Tracking screen and verify the fields.
Screen:  Screens-> Government Insuring > Insurance Tracking
Test Data: nan
Expected Result: Below fields should be present:
• ‘Government insuring received date’ label is renamed to ‘Government Audit Received Date’.
• A new 'date picker' field with label ‘Post-Closing Reviewed Date’ should be present under existing ‘Agency Case Number’ field.
• ‘Government insuring review date’ label is renamed to ‘Government Insuring Review Date’.

Step 04
Description: Verify the below fields:
• Government Audit Received Date
• Post-Closing Reviewed Date
• Government Insuring Review Date
Screen:  Screens-> Government Insuring > Insurance Tracking
Test Data: nan
Expected Result: All the fields should be disabled.

Step 05
Description: Assign privilege 1016 to the IDUW user
Screen: nan
Test Data: nan
Expected Result: Privilege should be assigned.

Step 06
Description:

--- RAW DOC 6 ---
TestCaseId: 569523_WHL_01
Content Preview:

TestCase: 569523_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Pre-Conditions:
1. Channel: WHL
2. Product : FF30
3. Loan purpose: Purchase
4. Loan type: FHA
5. Loan Stage: Created
6. Number of borrowers: 3(Application 2)

=========== TEST STEPS ===========

Step 1
Description: Log in to UAT1 H2O-A
Screen: https://qch2o.caliberdirect.com
Test Data: nan
Expected Result: Login should be successful

Step 2
Description: Open a Loan which is created or existing as per the preconditions  
Screen: Dashboard
Test Data: nan
Expected Result: Loan should be opened and Loan summary page should be opened.

Step 3
Description: Hover over 'Tools' and verify the new Item 'Modernized Audit' is displayed.
Screen: Loan Header
Test Data: nan
Expected Result: The New Item 'Modernized Audit' should be displayed to the user when user hover over 'Tools'

Step 4
Description: Click on the Item 'Modernized Audit'.
Screen: Modernized Audit window
Test Data: nan
Expected Result: Verify the system opens a New Window.

Step 5
Description: Retain the Default value 'Select' on the view drop down.
Screen: Modernized Audit window
Test Data: nan
Expected Result: Default value 'Select'  should be retained on  the view drop down.

Step 6
Description: Click on the 'Search' text box and verify the items displayed in the search list.
Note : Search button should not be displayed.
Screen: Modernized Audit window
Test Data: nan
Expected Result: No Fields should be displayed in the search list as only the default value is selected.

Step 7
Description: Select the value 'Loan' from the view dropdown and verify the items displayed in the search list.
Screen: Modernized Audit window
Test Data: nan
Expected Result: Search List should display all the Loan Level Audit field Items mentioned in the US#569523 > 202308-release-Audit Fields Ready for Mapping-v2.xlsx in Alphabetical order (a - z).

Note: The Search List should also display the Loan Level Audit field Items deployed as part of July release. Please refe

--- RAW DOC 7 ---
TestCaseId: 718523_WHL_01
Content Preview:

TestCase: 718523_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a Loan from Broker Portal with the below pre-conditions:
1. Channel: WHL
2. Loan Purpose: Refinance
3. Loan Type: FHA
4. Product: Any 
5. Loan stage should be UW Submitted
6. Early Disclosure should be generated, sent via eSign ,Esign should be completed and disclosure should be received. 


=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment
Screen: Login Page
Test Data: https://uath2o.newrez.com
Expected Result: Login should be successful

Step 2
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to DOCS --> Closing Details and check the below dates:
• Earliest Consummation Date
• Most Recent LE Receipt Date
• Rescission Date
• Agent Disbursement Date
Note: Enter some date in Agent Disbursement date if date is not present.
Screen: DOCS --> Closing Details
Test Data: nan
Expected Result: Mentioned dates should be present under DOCS --> Closing Details.

Step 4
Description: Navigate to Tools --> Modernized Audit and verify the below field values:
• Earliest Consummation Date
• Most Recent LE Receipt Date
• Rescission Date
• Agent Disbursement Date
Screen: Modernized Audit
Test Data: nan
Expected Result: Previous values should be displayed as blank and New values should be displayed as per step 3

Step 5
Description: Navigate to DIS --> Generate Disclosure and change the "Receipt Date:" under Transaction History.
Screen: DIS --> Generate Disclosure
Test Data: nan
Expected Result: "Receipt Date" should be changed.

Step 6
Description: Navigate to DOCS --> Closing Details and verify the "Most Recent LE Receipt Date" is same as "Receipt Date". Again change the Agent Disbursement Date and Recession date by entering a different date.
Screen: DOCS --> Closing Details
Test Data: nan
Expected Result: Dates sh

--- RAW DOC 8 ---
TestCaseId: 673197_670270_665820_WHL_01
Content Preview:

TestCase: 673197_670270_665820_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Originate the loan from Broker portal:
https://uat-c01-brokerportal-ui.azurewebsites.net
1. Channel: WHL
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Document type: Any
5. SSN to be used: 005-72-2884
6. Loan Stage: Created.

=========== TEST STEPS ===========

Step 01
Description: Login to H2O-A user without privilege 1298
Screen: Login page
Test Data: https://qch2o.caliberdirect.com/
Expected Result: Login should be successful

Step 02
Description: Open a Loan which is created as per the preconditions 
Screen: Dashboard
Test Data: nan
Expected Result: Loan summary page should be opened.

Step 03
Description: Verify the loan header.
Note: Verify this step on all the screens (all 1003, 1008, DIS, DOCS, All the Left Menu screen, Brown screen)
Screen: Loan Summary
Test Data: nan
Expected Result: • "Servicing Fraud Alert" field along with the checkbox should be available on the loan header.
• "Servicing Fraud Alert" checkbox should be disabled.

Step 04
Description: Login to H2O-A user with privilege 1298
Screen: Login page
Test Data: https://qch2o.caliberdirect.com/
Expected Result: Login should be successful

Step 05
Description: Open a Loan which is created as per the preconditions 
Screen: Dashboard
Test Data: nan
Expected Result: Loan summary page should be opened.

Step 06
Description: Verify the loan header.
Note: Verify this step on all the screens (all 1003, 1008, DIS, DOCS, All the Left Menu screen, Brown screen)
Screen: Loan Summary
Test Data: nan
Expected Result: • "Servicing Fraud Alert" field along with the checkbox should be available on the loan header.
• "Servicing Fraud Alert" checkbox should be enabled.

Step 07
Description: Uncheck "Servicing Fraud Alert" field checkbox.
Screen: Loan Summary
Test Data: nan
Expected Result: Servicing Fraud Alert" field checkbox should be unchecked.

Step 08
Description: Navigate to Tools > Modernized Audit > Select "Loan" op

--- RAW DOC 9 ---
TestCaseId: 573135_WHL_02
Content Preview:

TestCase: 573135_WHL_02
Channels: WHL

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: FHA
4. Product Code: FF30
5. Loan Stage: "Created"
6. No. of Borrowers: 2





=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful 

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in the new window

Step 04
Description: Verify the Fields in new Modernized Audit Screen
Screen: Modernized Audit
Test Data: nan
Expected Result: The following Fields should be available in the new Modernized Audit screen:
1. Search Frame under Audit 
2. View dropdown with default selection as "Loan" 
3. Search text field labeled as Field 
4. "Date Occurred, From" Field 
5. "Date Occurred, To" Field
6. Search Button
7. Reset Filters Button

Step 05
Description: Click on "View" dropdown.
Screen: Modernized Audit
Test Data: nan
Expected Result: "(P)" should be displayed before primary borrower of 1st application and "View" dropdown should display following options in the order:
• Select
• Loan
• (P) Name of Primary Borrower of 1st Application
• Name of Co-Borrower of 1st Application
• Name of Borrower of 2nd Application.
• Name of Co-borrower of 2nd application.

Step 06
Description: Select "Loan" from "View" dropdown.
Screen: Modernized Audit
Test Data: nan
Expected Result: "Loan" from "View" dropdown should be selected.

Step 07
Description: Click on Field
Screen: Modernized Audit
Test Data: nan
Expected Result: "Search" field should display all loan level audit 
fields list sorted in alphabetical orde

--- RAW DOC 10 ---
TestCaseId: 567672_WHL_01
Content Preview:

TestCase: 567672_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Pre-Conditions:
1. Channel: WHL
2. Loan should have the below Audit entries starting from 7/14/2022 to 7/15/2023(within the given dates)
>MI Company Name
>Project Identifier
>FHLMC Project Review
>FNMA Project Review

Reference Screens:
>MI Company Name : 1008>Mortgage Insurance>Coverage>MI Company.
>Project Identifier : 1008>Appraisal and Property>Project Information>Project Identifier.
>FHLMC Project Review : 1008>Appraisal and Property>Project Information>Freddie Mac Project Review. 
>FNMA Project Review : 1008>Appraisal and Property>Project Information>Fannie Mae Project Review.

=========== TEST STEPS ===========

Step 1
Description: Log in to UAT1 H2O-A
Screen: https://qch2o.caliberdirect.com
Test Data: nan
Expected Result: Login should be successful

Step 2
Description: Open a Loan which is created or existing as per the preconditions  
Screen: Dashboard
Test Data: nan
Expected Result: Loan should be opened and Loan summary page should be opened.

Step 3
Description: Hover over 'Tools' and verify the new Item 'Modernized Audit' is displayed.
Screen: Loan Header
Test Data: nan
Expected Result: The New Item 'Modernized Audit' should be displayed to the user when user hover over 'Tools'

Step 4
Description: Click on the new Item 'Modernized Audit'.
Screen: Modernized Audit window
Test Data: nan
Expected Result: Verify the system opens a New Window.

Step 5
Description: Type 'MI Company Name' in Search field and click on Search Button
Screen: Modernized Audit window
Test Data: nan
Expected Result: Audit records of MI Company Name should be displayed

Step 6
Description: Note down the Previous value and New value.
Screen: Modernized Audit window
Test Data: nan
Expected Result: Previous value and New value should be noted down.

Step 7
Description: Type 'Project Identifier' in Search field and click on Search Button.
Screen: Modernized Audit window
Test Data: nan
Expected Result: Audit records of 

--- RAW DOC 11 ---
TestCaseId: 738152_WHL_02
Content Preview:

TestCase: 738152_WHL_02
Channels: WHL

Pre-Condition & Assumptions:
Create a new loan from Blue Print with the following pre-condition:
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: FHA
4. Product: FF30 (Lender Paid)
5. Loan Stage: Created


=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment 
Screen: Login Page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 2
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to Company Details and verify the "Lender-Paid Comp"
Screen: Loan Summary
Test Data: nan
Expected Result: "Lender-Paid Comp" field should have value and it should be disabled and locked

Step 4
Description: Navigate to Tools > Modernized Audit and verify the entry created for the field name "Lender Paid Compensation Percentage":
- User who made the change 
- Time Occurred 
- Previous Value 
- New Value 
- Field name as "Lender Paid Compensation Percentage"

Screen: Modernized Audit 
Test Data: nan
Expected Result: Record should be created for the field "Lender Paid Compensation Percentage" and previous value should be blank and new value should be as per the loan data


Step 5
Description: Navigate to Tools > Modernized Audit and verify the entry created for the field name "Compensation":
- User who made the change 
- Time Occurred 
- Previous Value 
- New Value 
- Field name as "Compensation"

Screen: Modernized Audit 
Test Data: nan
Expected Result: Record should be created for the field "Compensation" and previous value should be blank and new value should be as per the loan data


Step 6
Description: Navigate to Tools > Modernized Audit and verify the entry created for the field name "CompensationPlanName":
- User who made the change 
- Time Occurred 
- Previous Value 
- New Value 
- Field name as "CompensationPla

--- RAW DOC 12 ---
TestCaseId: 573135_WHL_01
Content Preview:

TestCase: 573135_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Document type: Full
5. Loan Stage: "Created" 
6. Product: CF30





=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful 

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in the new window

Step 04
Description: Verify the Fields in new Modernized Audit Screen
Screen: Modernized Audit
Test Data: nan
Expected Result: The following Fields should be available in the new Modernized Audit screen:
1. Search Frame under Audit 
2. View dropdown with default selection as "Loan" 
3. Search text field labeled as Field 
4. "Date Occurred, From" Field 
5. "Date Occurred, To" Field
6. Search Button
7. Reset Filters Button

Step 05
Description: Click on calendar icon next to "Date Occurred, From"
Screen: Modernized Audit
Test Data: nan
Expected Result: Calendar control should be opened

Step 06
Description: Select a date on the Calendar 

Screen: Modernized Audit
Test Data: nan
Expected Result: Date selected in Calendar should be loaded in "Date Occurred, From" field

Step 07
Description: Click on Search Button
Screen: Modernized Audit
Test Data: nan
Expected Result:  "Date Occurred, From can not be empty while Date Occurred, To is not. Please select both the dates or remove both" UI message should be displayed

Step 08
Description: Click on Ok
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be displayed

Step 09
Description: Remove the date

--- RAW DOC 13 ---
TestCaseId: 573135_WHL_03
Content Preview:

TestCase: 573135_WHL_03
Channels: WHL

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: VA
4. Document type: Full
5. Loan Stage: "Created" 
6. Product: VF30

Note: Changes has been made in the loan to display minimum 60 Audit Logs



=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful 

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in the new window

Step 04
Description: Verify the Fields in new Modernized Audit Screen
Screen: Modernized Audit
Test Data: nan
Expected Result: The following Fields should be available in the new Modernized Audit screen:
1. Search Frame under Audit 
2. View dropdown with default selection as "Loan" 
3. Search text field labeled as Field 
4. "Date Occurred, From" Field 
5. "Date Occurred, To" Field
6. Search Button
7. Reset Filters Button

Step 05
Description: Verify the Audit Logs
Screen: Modernized Audit
Test Data: nan
Expected Result: 50 loan level audited entries should be displayed in Audit logs by default

Note: For loan having less than 50 loan level audit records, only those number of records should be displayed.  

Step 06
Description: Verify Records per page dropdown at footer 
Screen: Modernized Audit
Test Data: nan
Expected Result: Records per page dropdown at footer should be defaulted with value of 50  

Step 07
Description: Change per page dropdown selection for Records per page and verify dropdown changes 
Screen: Modernized Audit
Test Data: nan
Expected Result: Screen size should load records based on the selected page

--- RAW DOC 14 ---
TestCaseId: 623485_623489_WHL_01
Content Preview:

TestCase: 623485_623489_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
1. Channel: Retail.
2. Loan Purpose: Purchase.
3. Loan Type: FHA.
4. Product: FF30.
5. Loan Stage: "Created".

User should have privilege 1280.
Loan should be created after Nov release deployment.

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT environment.
Screen: Dashboard
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened.

Step 03
Description: Navigate to Screens > Pre-Underwriting > Pre-Underwriting main > Expiration Dates and verify the fields.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Below new fields should be displayed under Expiration Dates section.
• Income Document Date
• Assets Document Date

Step 04
Description: Click on the text box for "Income Document Date" and "Asset Document Date" field.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Calendar should be opened.

Step 05
Description: Select the dates for each field and click on save.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Dates should be selected for each field.

Step 06
Description: Navigate to Tools > Modernized Audit and verify the 'Income Document Expiration' & 'Assets Document Date' record
Screen: Modernized Audit
Test Data: nan
Expected Result: Records should be display pervious value and new value correctly.

Step 07
Description: Close the Mod Audit and Navigate to Tools >  Audit and verify the 'Income Document Expiration'& 'Assets Document Date' record
Screen: Audit pop up
Test Data: nan
Expected Result: Records should be display pervious value and new value correctly.

Step 08
Description: Navigate to 1008 > UW analysis > Expiration Dates and verify below fields.
• Income Document Date
• Assets Document Date
Screen: UW anal

--- RAW DOC 15 ---
TestCaseId: 565797_WHL_01
Content Preview:

TestCase: 565797_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create two new loans in Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: "Created"

Note: Testing should be done in Chrome browser

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 environment.
Screen: https://qch2o.caliberdirect.com
Test Data: nan
Expected Result: Login should be successful.

Step 02
Description: Open the first loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened fort the first loan.

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Audit
Test Data: nan
Expected Result: User should be navigated to "Modernized 
Audit" screen.

Step 04
Description: Don't close the "Modernized Audit" screen and navigate back to "Loan Summary" screen of the first loan.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened for the first loan.

Step 05
Description: Enter the second loan number in the 
"Search" box and click on "Go" button.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened for the second loan.

Step 06
Description: Navigate back to unclosed "Modernized 
Audit" screen of the first loan.
Screen: Audit
Test Data: nan
Expected Result: System should display a warning pop up with verbiage "Loan has changed. Click 'Ok' to refresh." and the pop up should also display "Ok" button.

Step 07
Description: Click "Ok" button on the pop up.
Screen: Audit
Test Data: nan
Expected Result: User should be able to verify the following:
• System should sync "Modernized Audit" screen to current loan (second loan).
• "Modernized Audit" screen should reset to its default state.
• "Select" should be selected as default value in "View" dropdown.
• Loan number field should reflect the current (second) loan's loan num

--- RAW DOC 16 ---
TestCaseId: 623485_623489_WHL_Pipeline_01
Content Preview:

TestCase: 623485_623489_WHL_Pipeline_01
Channels: WHL

Pre-Condition & Assumptions:
1. Channel: Retail.
2. Loan Purpose: Purchase.
3. Loan Type: Conventional..
4. Product: CF30.
5. Loan Stage: "Created".

User should have privilege 1280.
Loan should be created before Nov release deployment.

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT environment.
Screen: Dashboard
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened.

Step 03
Description: Navigate to Screens > Pre-Underwriting > Pre-Underwriting main > Expiration Dates and verify the fields.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Below new fields should be not displayed.
• Income Document Date
• Assets Document Date

Step 04
Description: Navigate to 1008 > UW analysis > Expiration Dates and verify below fields.
• Income Document Date
• Assets Document Date
Screen: UW analysis
Test Data: nan
Expected Result: Below fields should not be displayed under Expired Date section.
• Income Document Date
• Assets Document Date

Step 05
Description: Navigate to Tools > Modernized Audit and verify the 'Income Document Expiration' & 'Assets Document Date' record
Screen: Modernized Audit
Test Data: nan
Expected Result: Records should not be displayed

Step 06
Description: Close the Mod Audit and Navigate to Tools >  Audit and verify the 'Income Document Expiration'& 'Assets Document Date' record
Screen: Audit pop up
Test Data: nan
Expected Result: Records should not be displayed

Step 07
Description: Logout from H2O A.
Screen: Dashboard
Test Data: nan
Expected Result: Log out should be successful.


--- RAW DOC 17 ---
TestCaseId: 658373_WHL_01
Content Preview:

TestCase: 658373_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a new loan with the following pre-conditions:
1. Channel: WHL
2. Loan Purpose: Refinance
3. Loan Type: FHA
4. Loan Product: FF30




=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 Environment
Screen: Login Page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 02
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 03
Description: Navigate to 1008 > Appraisal & Property screen
Screen: Appraisal & Property screen
Test Data: nan
Expected Result: Appraisal & Property screen should be opened.

Step 04
Description: Verify the below new fields under "Project Information section":
• 'CPM Certification ID #'
• 'Freddie PAR #'
• 'PWR Number'
Screen: Appraisal & Property screen
Test Data: nan
Expected Result: Below new fields should be visible under "Project Information section":
• 'CPM Certification ID #'   (underneath 'CPM Project ID #' field)
• 'Freddie PAR #'                 (underneath the new 'CPM Certification ID #' field)
• 'PWR Number'                  (underneath 'Target Agency Info' dropdown)

Step 05
Description: Enter Special characters, alphabets & spaces in the new field 'CPM Certification ID #' 
Screen: Appraisal & Property screen
Test Data: nan
Expected Result: New field 'CPM Certification ID #' should not allow user to enter Special characters, alphabets and spaces.

Step 06
Description: Enter numeric characters in the new field 'CPM Certification ID #' 
Screen: Appraisal & Property screen
Test Data: nan
Expected Result: New field 'CPM Certification ID #' should allow user to enter numeric characters and maximum 10 characters.

Step 07
Description: Enter Special characters, alphabets & spaces in the new field 'Freddie PAR #' 
Screen: Appraisal & Property screen
Test Data: nan
Expe

--- RAW DOC 18 ---
TestCaseId: 561836_WHL_01
Content Preview:

TestCase: 561836_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a new loan using Mismo 3.4 XML file
1. Channel: WHL
2. Loan Purpose: Refinance
3. Loan Type: VA
4. Product: VF30
5. Stage: Created
6. No. of Borrowers: 2






=========== TEST STEPS ===========

Step 01
Description: Login to H2O Application in UAT1 environment 

Screen: H2OA login page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.


Step 02
Description: Open the loan which is created as per the Pre-Condition.  
Screen: H2O-A Dashboard
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 03
Description: Navigate to "1003" --> "Loan Summary" screen and verify that some amount is present in "Total Loan Amount".
Screen: Loan Summary
Test Data: nan
Expected Result: Value should be present for the field.

Step 04
Description: Navigate to "1003" -->"Emp, Inc & Housing" screen and note the income value for Borrower and Co-Borrower.
Screen: Emp, Inc & Housing
Test Data: nan
Expected Result: Monthly Income for both Borrower and Co-Borrower should be noted down.

Step 05
Description: Navigate to Tools --> Modernized Audit and click on Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in a new window

Step 06
Description: Verify the option "Loan" defaulted to "View" field. 
Screen: Modernized Audit
Test Data: nan
Expected Result: "Loan" should be defaulted for "View" field.

Step 07
Description: Verify the Audit logs
Screen: Modernized Audit
Test Data: nan
Expected Result: 1. "Total Loan Amt (Calc)" should be displayed in the result grid along with other fields.
2. "Previous Value" should be NULL and "New Value" should be current value for the "Field" value "Total Loan Amt (Calc)".


Step 08
Description: Input the below detail in Audit screen:
• Type "Total" and select "Total Loan Amt (Calc)" for "Field" and click on search.
Screen: nan
Test Data: nan
Expected 

--- RAW DOC 19 ---
TestCaseId: 559659_WHL_01
Content Preview:

TestCase: 559659_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a loan with Mismo 3.4 XML file which has Type of Property as "Detached "
Pre-Conditions:
1. Channel: WHL
2. Loan Type: Conventional
3. Product Code: CF30
4. Loan Stage: "Created "

=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT1 Environment
Screen: https://qch2o.caliberdirect.com
Test Data:  
Expected Result: Login should be successful

Step 2
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data:  
Expected Result: Loan Summary screen should be opened

Step 3
Description: Verify the Type of Property and change it to "Condominium"
Screen: Property Details section
Test Data:  
Expected Result: Type of Property should be "Detached" and it should be changed to "Condominium"

Step 4
Description: Verify the "Base Loan Amount" and change it to other value. 
Screen: Loan Summary
Test Data:  
Expected Result: Base Loan Amount should be modified 

Step 5
Description: Verify the "Loan Rate" and change it to other value.
Screen: Loan Summary
Test Data:  
Expected Result: Loan rate should be modified

Step 6
Description: Verify the "Term" and change it to some other date.
Screen: Loan Summary
Test Data:  
Expected Result: Loan Term should be modified

Step 7
Description: Click on Save button
Screen: Loan Summary
Test Data:  
Expected Result: Updated data should be saved

Step 8
Description: Navigate Docs > Closing Details and enter the note Date
Screen: Docs > Closing Details
Test Data:  
Expected Result: Note date should be entered

Step 9
Description: Click on Save button
Screen: Docs > Closing Details
Test Data:  
Expected Result: Updated data should be saved

Step 10
Description: Navigate to Tools > Modernized Audit 
Screen: Modernized Audit 
Test Data:  
Expected Result: Modernized Audit screen should be displayed

Step 11
Description: Verify the PropertyTypeID field as below:
Previous Value: Detached
New Value: Condomi

--- RAW DOC 20 ---
TestCaseId: 567881_WHL_01
Content Preview:

TestCase: 567881_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a new loan using Mismo 3.4 XML file
1. Channel: WHL
2. Loan Purpose: Refinance
3. Loan Type: VA
4. Product: VF30
5. Stage: Created
6. Browser: Edge




=========== TEST STEPS ===========

Step 01
Description: Login to H2O Application in UAT1 environment 

Screen: H2OA login page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.


Step 02
Description: Open the loan which is created as per the Pre-Condition.  
Screen: H2O-A Dashboard
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 03
Description: Navigate to Tools --> Modernized Audit and click on Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in a new window

Step 04
Description: Input the below detail in Audit screen.
• Select "View" as Loan
• Input "Search" as Property Type
Screen: nan
Test Data: nan
Expected Result: Record should be displayed for loan property type.

Step 05
Description: Navigate back to H2O screen without closing the Modernized Audit window.
Screen: Loan Summary
Test Data: nan
Expected Result: Navigation should be done without closing the Modernized Audit window.

Step 06
Description: Again navigate to Tools --> Modernized Audit and click on Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: A new Modernized Audit window should not be opened and existing Modernized Audit window should be refreshed.

Step 07
Description: Close the Modernized Audit window.
Screen: Modernized Audit
Test Data: nan
Expected Result:  Modernized Audit window should be closed.

Step 08
Description: Navigate to any other screen in H2OA

Ex: 1008 -> Decisions and Conditions
Screen: Decisions and Conditions
Test Data: nan
Expected Result: 1008 -> Decisions and Conditions screen should be opened.

Step 09
Description: Navigate to Tools --> Modernized Audit and click on Modernized A

--- RERANKED ORDER ---
Rank 1: 564936_564981_WHL_01
Rank 2: 569523_WHL_01
Rank 3: 579578_WHL_01
Rank 4: 573135_WHL_02
Rank 5: 573135_WHL_01
Rank 6: 573135_WHL_03
Rank 7: 565797_WHL_01
Rank 8: 561836_WHL_01
Rank 9: 567881_WHL_01
Rank 10: 567672_WHL_01
Rank 11: 559659_WHL_01
Rank 12: 647338_647343_WHL_01
Rank 13: 718523_WHL_01
Rank 14: 673197_670270_665820_WHL_01
Rank 15: 738152_WHL_02
Rank 16: 623485_623489_WHL_01
Rank 17: 623485_623489_WHL_Pipeline_01
Rank 18: 658373_WHL_01
Rank 19: 738152_WHL_01
Rank 20: 579217_WHL_01

--- STRUCTURED EXTRACTION ---
{
  "scenario": "Verify audit logs and borrower-level audit fields in H2O-A application for a loan created as per pre-conditions.",
  "script": "564936_564981_WHL_01",
  "precondition": "Create a new loan in Mismo 3.4 XML file with the following details: Channel: WHL, Loan Purpose: Purchase, Loan Type: Conventional, Product Code: CF30, Loan Stage: 'Created', No. of Applications: 2, No. of Borrowers in 1st Application: 2, No. of Borrowers in 2nd Application: 2.",
  "steps": [
    {
      "stepNo": "01",
      "description": "Log in to H2O-A in UAT1 environment.",
      "expectedResult": "Login should be successful."
    },
    {
      "stepNo": "02",
      "description": "Open the loan which is created as per the Pre-Conditions.",
      "expectedResult": "Loan summary screen should be opened."
    },
    {
      "stepNo": "03",
      "description": "Navigate to Tools->Modernized Audit.",
      "expectedResult": "'Search' button should not be present in 'Modernized Audit' screen."
    },
    {
      "stepNo": "04",
      "description": "Verify default selection value of 'View' dropdown.",
      "expectedResult": "'Select' should be selected as default in 'View' dropdown."
    },
    {
      "stepNo": "05",
      "description": "Click on 'View' dropdown.",
      "expectedResult": "User should verify that '(P)' is displayed before primary borrower of 1st application and 'View' dropdown should display following options in the order: Select, Loan, (P) Name of Primary Borrower of 1st Application, Name of Co-Borrower of 1st Application, Name of Borrower of 2nd Application, Name of Co-borrower of 2nd application."
    },
    {
      "stepNo": "06",
      "description": "Select 'Loan' from 'View' dropdown.",
      "expectedResult": "User should be able to select 'Loan' from 'View' dropdown."
    },
    {
      "stepNo": "07",
      "description": "Verify audit logs.",
      "expectedResult": "Audit logs should not be displayed until user clicks on 'Search' field."
    },
    {
      "stepNo": "08",
      "description": "Click on 'Search' field.",
      "expectedResult": "'Search' field should display all loan level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "09",
      "description": "Verify all loan level audit field logs.",
      "expectedResult": "User should be able to verify all loan level audit field logs."
    },
    {
      "stepNo": "10",
      "description": "Enter 'ap' in the search field.",
      "expectedResult": "System should not filter audit fields list matching the characters 'ap'."
    },
    {
      "stepNo": "11",
      "description": "Enter 'appr' in the search field.",
      "expectedResult": "System should filter audit fields list matching the characters 'appr'."
    },
    {
      "stepNo": "12",
      "description": "Enter 'Type of Property' in the search field and select 'Type of Property'.",
      "expectedResult": "User should verify that space is also counted as a character. System should populate all the records of the selected field 'Type of Property'."
    },
    {
      "stepNo": "13",
      "description": "Select primary borrower of 1st application from 'View' dropdown.",
      "expectedResult": "User should be able to select primary borrower of 1st application from 'View' dropdown."
    },
    {
      "stepNo": "14",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all primary borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "15",
      "description": "Select co-borrower of 1st application from 'View' dropdown.",
      "expectedResult": "User should be able to select co-borrower of 1st application from 'View' dropdown."
    },
    {
      "stepNo": "16",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all co-borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "17",
      "description": "Select borrower of 2nd application from 'View' dropdown.",
      "expectedResult": "User should be able to select borrower of 2nd application from 'View' dropdown."
    },
    {
      "stepNo": "18",
      "description": "Verify audit logs.",
      "expectedResult": "Audit logs should not be displayed until user clicks on 'Search' field."
    },
    {
      "stepNo": "19",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "20",
      "description": "Select co-borrower of 2nd application from 'View' dropdown.",
      "expectedResult": "User should be able to select co-borrower of 2nd application from 'View' dropdown."
    },
    {
      "stepNo": "21",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all co-borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "22",
      "description": "Logout from H2O-A.",
      "expectedResult": "Log out should be successful."
    }
  ]
}

--- FINAL SELECTED STRUCTURED DOC ---
{
  "scenario": "Verify audit logs and borrower-level audit fields in H2O-A application for a loan created as per pre-conditions.",
  "script": "564936_564981_WHL_01",
  "precondition": "Create a new loan in Mismo 3.4 XML file with the following details: Channel: WHL, Loan Purpose: Purchase, Loan Type: Conventional, Product Code: CF30, Loan Stage: 'Created', No. of Applications: 2, No. of Borrowers in 1st Application: 2, No. of Borrowers in 2nd Application: 2.",
  "steps": [
    {
      "stepNo": "01",
      "description": "Log in to H2O-A in UAT1 environment.",
      "expectedResult": "Login should be successful."
    },
    {
      "stepNo": "02",
      "description": "Open the loan which is created as per the Pre-Conditions.",
      "expectedResult": "Loan summary screen should be opened."
    },
    {
      "stepNo": "03",
      "description": "Navigate to Tools->Modernized Audit.",
      "expectedResult": "'Search' button should not be present in 'Modernized Audit' screen."
    },
    {
      "stepNo": "04",
      "description": "Verify default selection value of 'View' dropdown.",
      "expectedResult": "'Select' should be selected as default in 'View' dropdown."
    },
    {
      "stepNo": "05",
      "description": "Click on 'View' dropdown.",
      "expectedResult": "User should verify that '(P)' is displayed before primary borrower of 1st application and 'View' dropdown should display following options in the order: Select, Loan, (P) Name of Primary Borrower of 1st Application, Name of Co-Borrower of 1st Application, Name of Borrower of 2nd Application, Name of Co-borrower of 2nd application."
    },
    {
      "stepNo": "06",
      "description": "Select 'Loan' from 'View' dropdown.",
      "expectedResult": "User should be able to select 'Loan' from 'View' dropdown."
    },
    {
      "stepNo": "07",
      "description": "Verify audit logs.",
      "expectedResult": "Audit logs should not be displayed until user clicks on 'Search' field."
    },
    {
      "stepNo": "08",
      "description": "Click on 'Search' field.",
      "expectedResult": "'Search' field should display all loan level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "09",
      "description": "Verify all loan level audit field logs.",
      "expectedResult": "User should be able to verify all loan level audit field logs."
    },
    {
      "stepNo": "10",
      "description": "Enter 'ap' in the search field.",
      "expectedResult": "System should not filter audit fields list matching the characters 'ap'."
    },
    {
      "stepNo": "11",
      "description": "Enter 'appr' in the search field.",
      "expectedResult": "System should filter audit fields list matching the characters 'appr'."
    },
    {
      "stepNo": "12",
      "description": "Enter 'Type of Property' in the search field and select 'Type of Property'.",
      "expectedResult": "User should verify that space is also counted as a character. System should populate all the records of the selected field 'Type of Property'."
    },
    {
      "stepNo": "13",
      "description": "Select primary borrower of 1st application from 'View' dropdown.",
      "expectedResult": "User should be able to select primary borrower of 1st application from 'View' dropdown."
    },
    {
      "stepNo": "14",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all primary borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "15",
      "description": "Select co-borrower of 1st application from 'View' dropdown.",
      "expectedResult": "User should be able to select co-borrower of 1st application from 'View' dropdown."
    },
    {
      "stepNo": "16",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all co-borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "17",
      "description": "Select borrower of 2nd application from 'View' dropdown.",
      "expectedResult": "User should be able to select borrower of 2nd application from 'View' dropdown."
    },
    {
      "stepNo": "18",
      "description": "Verify audit logs.",
      "expectedResult": "Audit logs should not be displayed until user clicks on 'Search' field."
    },
    {
      "stepNo": "19",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "20",
      "description": "Select co-borrower of 2nd application from 'View' dropdown.",
      "expectedResult": "User should be able to select co-borrower of 2nd application from 'View' dropdown."
    },
    {
      "stepNo": "21",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all co-borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "22",
      "description": "Logout from H2O-A.",
      "expectedResult": "Log out should be successful."
    }
  ]
}

====================================
CHANNEL: DTC
====================================

Retrieved 20 documents


--- RAW DOC 1 ---
TestCaseId: 718524_DTC_01
Content Preview:

TestCase: 718524_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
Create Loan using Loan Advisor from H2O-A with below pre-conditions.
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: Any
4. Product: Any
5. Loan stage: Application Accepted.
6. No of Borrowers: 2, 1Application
7.  DIS > LE Fees > Rate Information-
"Borrower will receive a __  %  /  $  
$0.00 __ Lender Credit for the interest rate of __ % " radio button should be selected.

=========== TEST STEPS ===========

Step 01
Description: Log in to UAT1 H2O-A
Screen: Login page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 02
Description: Open a Loan which is created as per the preconditions.
Screen: Dashboard
Test Data: nan
Expected Result: Loan should be opened and Loan summary page should be opened.

Step 03
Description: Navigate to Audit and Search field YSPPointsAtNoLock in search box
Screen: Audit
Test Data: nan
Expected Result: YSPPointsAtNoLock should be present as Field name along with User, Time Occurred, Previous Value, New Value and New value should match the percent value entered in Rate information.

Step 04
Description: Navigate to DIS > LE Fees screen and change the rate information value and click on master Save.
Screen: nan
Test Data: nan
Expected Result: Rate information value should be changed and saved.

Step 05
Description: Navigate to Audit and Search field YSPPointsAtNoLock in search box
Screen: Audit
Test Data: nan
Expected Result: YSPPointsAtNoLock should be present as Field name along with User, Time Occurred, Previous Value, New Value and New value should match the percent value entered in Rate information.

Step 06
Description: Search field YSPPointsAtNoLock in search box ApplicationTakenByMediumID. 
Screen: Audit
Test Data: nan
Expected Result: ApplicationTakenByMediumID should not be present.

Step 07
Description: Navigate to DIS > Generate Disclosures screen > Generate and send Early Disclosures.
Screen: Generate Disclosures
T

--- RAW DOC 2 ---
TestCaseId: 579578_DTC_01
Content Preview:

TestCase: 579578_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
Create a new loan using Mismo 3.4 XML file
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: FHA
4. Product: FF30
5. Stage: Created
6. Number of Borrowers: Any





=========== TEST STEPS ===========

Step 1
Description: Login to H2O Application in UAT1 environment 

Screen: H2OA login page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.


Step 2
Description: Open the loan which is created as per the Pre-Condition. 
Screen: H2O-A Dashboard
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to "1003" --> "Loan Summary" screen and verify that values are available in "P&I:" and "Housing Exp." field under Calculations section.
Screen: Loan Summary
Test Data: nan
Expected Result: Value should be available for both the fields.

Step 4
Description: Navigate to Tools --> Modernized Audit and click on Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in a new window

Step 5
Description: Verify the option "Loan" defaulted to "View" field. 
Screen: Modernized Audit
Test Data: nan
Expected Result: "Loan" should be defaulted for "View" field.

Step 6
Description: Select "Proposed Housing Expense Total (Calc)" and "Proposed Housing - First Mortgage (P&I) (Calc)" for "Field" and click on Search.
Screen: Modernized Audit
Test Data: nan
Expected Result: Only "Proposed Housing Expense Total (Calc)" and "Proposed Housing - First Mortgage (P&I) (Calc)" should not be displayed in the result grid


Step 7
Description: Stage the loan to Application Accepted stage
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Stage should be Application Accepted

Step 8
Description: Navigate to Tools --> Modernized Audit and click on Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in a ne

--- RAW DOC 3 ---
TestCaseId: 564936_564981_DTC_01
Content Preview:

TestCase: 564936_564981_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
Create a new loan in Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: RTL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: "Application Accepted"
6. No. of Application: 1
7. No. of Borrowers in 1st Application: 1
For eg: 1st application has Alice Firstimer

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 environment.
Screen: https://qch2o.caliberdirect.com
Test Data: nan
Expected Result: Login should be successful.

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened.

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Audit
Test Data: nan
Expected Result: "Search" button should not be present in "Modernized Audit" screen.

Step 04
Description: Verify default selection value of "View" dropdown.
Screen: Audit
Test Data: nan
Expected Result: "Select" should be selected as default in "View" dropdown.

Step 05
Description: Click on "View" dropdown.
Screen: Audit
Test Data: nan
Expected Result: User should verify that "(P)" is displayed before primary borrower of 1st application and "View" dropdown should display following options in the order:
• Select
• Loan
• (P) Name of Primary Borrower of 1st Application

Step 06
Description: Select "Loan" from "View" dropdown.
Screen: Audit
Test Data: nan
Expected Result: User should be able to select "Loan" from "View" dropdown.

Step 07
Description: Verify audit logs.
Screen: Audit
Test Data: nan
Expected Result: Audit logs should not be displayed until user clicks on "Search" field.

Step 08
Description: Click on "Search" field.
Screen: Audit
Test Data: nan
Expected Result: "Search" field should display all loan level audit fields list sorted in alphabetical order(A-Z).

Step 09
Description: Verify all loan level audit field logs.
Note: Please refer to 

--- RAW DOC 4 ---
TestCaseId: 647338_647343_DTC_Pipeline_01
Content Preview:

TestCase: 647338_647343_DTC_Pipeline_01
Channels: DTC

Pre-Condition & Assumptions:
1. The loan should be part of the new model under the H2O digital workflow 
3. Ops Center: DTC Ops Admin
4. Loan Type - Conventional
5. Loan Purpose - Refinance
6. Loan Product - CF30
7. H2O-D Processing and UW Flag should be ‘Y’.                     
8. Loan Stage - "Condition in Review- UW".
9. Loan should have CRUW activity assigned to a User which has all CRUW skill and does not have privilege 1016.

Loan should be a pipeline Loan.

=========== TEST STEPS ===========

Step 01
Description: Login to H2O-D user dashboard as assigned CRUW activity and click on Loan number under "Condition review" queue
Screen: CRUW activity
Test Data: Https://uat-h2od.caliberhomeloans.com/
Expected Result: CRUW activity should be opened.

Step 02
Description: Expand Pre Condition Review section and click on Review loan details.
Screen: CRUW activity
Test Data: Loan Number:
Expected Result: 1003-> Loan Summary screen should be opened in the middle panel.

Step 03
Description: Navigate to Screens-> Government Insuring > Insurance Tracking screen and verify the fields.
Screen:  Screens-> Government Insuring > Insurance Tracking
Test Data: nan
Expected Result: Below fields should be present:
• ‘Government insuring received date’ label is renamed to, ‘Government Audit Received Date’.
• A new 'date picker' field with label ‘Post-Closing Reviewed Date’ should be present under existing ‘Agency Case Number’ field.
• ‘Government insuring review date’ label is renamed to, ‘Government Insuring Review Date’.

Step 04
Description: Verify the Below fields:
• Government Audit Received Date
• Post-Closing Reviewed Date
• Government Insuring Review Date
Screen:  Screens-> Government Insuring > Insurance Tracking
Test Data: nan
Expected Result: All the fields should be disabled and value should be null.

Step 05
Description: Assign privilege 1016 to the IDUW user
Screen: nan
Test Data: nan
Expected Result: Privilege s

--- RAW DOC 5 ---
TestCaseId: 738152_DTC_01
Content Preview:

TestCase: 738152_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
Create a new loan from Ignite Portal with the following pre-condition:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product: CF30 
5. Loan Stage: Application Accepted


=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment 
Screen: Login Page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 2
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to Electronic Delivery and Borrower Auth & Cert and verify the "Enable Electronic Disclosures" checkbox
Screen: Loan Summary
Test Data: nan
Expected Result: "Enable Electronic Disclosures" checkbox should be unchecked

Step 4
Description: Check the "Enable Electronic Disclosures" checkbox
Screen: Generate DOCS
Test Data: nan
Expected Result: "Enable Electronic Disclosures" checkbox should be checked

Step 5
Description: Navigate to Tools > Modernized Audit and verify the entry created for the field name "Electronic Consent":
- User who made the change 
- Time Occurred 
- Previous Value 
- New Value 
- Field name as "Electronic Consent"

Screen: Modernized Audit 
Test Data: nan
Expected Result: Record should be created for the field "Electronic Consent" and previous value should be false and new value should be true


Step 6
Description: Uncheck the "Enable Electronic Disclosures" checkbox
Screen: Loan Summary
Test Data: nan
Expected Result: "Enable Electronic Disclosures" checkbox should be unchecked

Step 7
Description: Navigate to Tools > Modernized Audit and verify the entry created for the field name "Electronic Consent":
- User who made the change 
- Time Occurred 
- Previous Value 
- New Value 
- Field name as "Electronic Consent"

Screen: Modernized Audit 
Test Data: nan
Expected Result: Record sho

--- RAW DOC 6 ---
TestCaseId: 680177_679185_DTC_02
Content Preview:

TestCase: 680177_679185_DTC_02
Channels: DTC

Pre-Condition & Assumptions:
Create a loan in H2O-A as per below pre-conditions:
1. Channel: DTC 
2. Loan Purpose: Refinance
3. Loan Type: FHA
4. Product Code: FF30
5. Loan Stage: "Application Accepted"
6. SSP: Mortgage Connect, LP
7. LPA should be run.
8. User should have Privilege 1301.
9. Subject Property Address - 100 Park Road 1A, Bastrop, TX, 78602 (Test Data that gives DU Title Waiver as Eligible)
10. Loan creation date should be such that it fllls within the waiver functionality roll out date.

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment 
Screen: Login Page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 02
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 03
Description: Navigate to 1008 > UW Analysis > AU/DU Findings section
Screen: UW Analysis
Test Data: nan
Expected Result: Title Waiver' field should be present with value as 'None' and with Lock Icon next to it but the field and Lock should be disabled.

Step 04
Description: Logout and Login without Privilege 1301 and Navigate to AUS > Request tab and Submit AUS request for both DU and LPA (Multi- AUS)

Screen: AUS
Test Data: nan
Expected Result: AUS should be run and LPA should be should be selected as Recommendation with Waiver as None.

Step 05
Description: Navigate to 1008 > UW Analysis > AU/DU Findings section
Screen: UW Analysis
Test Data: nan
Expected Result: Title Waiver' field should be present with value as 'None' and with Lock Icon next to it but the field and Lock should be disabled.

Step 06
Description: Navigate to AUS > Request tab and Select DU as Recommendation.
Screen: AUS
Test Data: nan
Expected Result: DU should be should be selected as Recommendation.

Step 07
Description: Logout and Login with Privilege 1301 and Na

--- RAW DOC 7 ---
TestCaseId: 573135_DTC_02
Content Preview:

TestCase: 573135_DTC_02
Channels: DTC

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: FHA
4. Product Code: FF30
5. Loan Stage: "Application Accepted"
6. No. of Borrowers:1





=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful 

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in the new window

Step 04
Description: Verify the Fields in new Modernized Audit Screen
Screen: Modernized Audit
Test Data: nan
Expected Result: The following Fields should be available in the new Modernized Audit screen:
1. Search Frame under Audit 
2. View dropdown with default selection as "Loan" 
3. Search text field labeled as Field 
4. "Date Occurred, From" Field 
5. "Date Occurred, To" Field
6. Search Button
7. Reset Filters Button

Step 05
Description: Click on "View" dropdown.
Screen: Modernized Audit
Test Data: nan
Expected Result: "(P)" should be displayed before primary borrower of 1st application and "View" dropdown should display following options in the order:
• Select
• Loan
• (P) Name of Primary Borrower of 1st Application
• Name of Co-Borrower of 1st Application
• Name of Borrower of 2nd Application.
• Name of Co-borrower of 2nd application.

Step 06
Description: Select "Loan" from "View" dropdown.
Screen: Modernized Audit
Test Data: nan
Expected Result: "Loan" from "View" dropdown should be selected.

Step 07
Description: Click on Field
Screen: Modernized Audit
Test Data: nan
Expected Result: "Search" field should display all loan level audit 
fields list sorted in alph

--- RAW DOC 8 ---
TestCaseId: 673197_670270_665820_DTC_01
Content Preview:

TestCase: 673197_670270_665820_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
Create a new loan in Ignite with following pre-conditions.
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: FHA
4. Product Code: Any
5. Loan Stage: "UW Submitted"
6. H2O-D Processing and Underwriting Flag should be ‘Y’ 
7. IDUW activity should present on Manger DB and assigned to user.


=========== TEST STEPS ===========

Step 01
Description: Login to H2O-D as above Underwriter user without privilege 1298
Screen: Login page
Test Data: https://uat-h2od.caliberhomeloans.com
Expected Result: Login should be successful

Step 02
Description: Navigate to 'Initial Decision Activities Assigned to Me' section > click on loan number > Go to step Credit and click on "Verify Loan Details"
Screen: Dashboard
Test Data: nan
Expected Result: Loan summary page should be opened in Middle panel.

Step 03
Description: Verify the loan header.

Note: Verify this step on all the screens (all 1003, 1008, DIS, DOCS, All the Left Menu screen, Brown screen)
Screen: Loan Summary
Test Data: nan
Expected Result: • "Servicing Fraud Alert" field along with the checkbox should be available on the loan header.
• "Servicing Fraud Alert" checkbox should be disabled.

Step 04
Description: Login to H2O-D as above Underwriter user with privilege 1298
Screen: Login page
Test Data: https://uat-h2od.caliberhomeloans.com
Expected Result: Login should be successful

Step 05
Description: Navigate to 'Initial Decision Activities Assigned to Me' section > click on loan number > Go to step Credit and click on "Verify Loan Details"
Screen: Dashboard
Test Data: nan
Expected Result: Loan summary page should be opened in Middle panel.

Step 06
Description: Verify the loan header.
Note: Verify this step on all the screens (all 1003, 1008, DIS, DOCS, All the Left Menu screen, Brown screen)
Screen: Loan Summary
Test Data: nan
Expected Result: • "Servicing Fraud Alert" field along with the checkbox should be available on the loan 

--- RAW DOC 9 ---
TestCaseId: 718516_DTC_01
Content Preview:

TestCase: 718516_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
Create Loan in Ignite with the below pre-conditions:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: VA
4. Product: Any 
5. Loan stage should be UW Submitted
6. Early Disclosure should be generated, sent via eSign ,Esign should be completed and disclosure should be received. 

=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment
Screen: Login Page
Test Data: https://uath2o.newrez.com
Expected Result: Login should be successful

Step 2
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to DOCS --> Closing Details and check the below dates:
• Earliest Consummation Date
• Most Recent LE Receipt Date
• Rescission Date
• Agent Disbursement Date
Note: Enter some date in Agent Disbursement date if date is not present.
Screen: DOCS --> Closing Details
Test Data: nan
Expected Result: Mentioned dates should be present under DOCS --> Closing Details.

Step 4
Description: Navigate to Tools --> Modernized Audit and verify the below field values:
• Earliest Consummation Date
• Most Recent LE Receipt Date
• Rescission Date
• Agent Disbursement Date
Screen: Modernized Audit
Test Data: nan
Expected Result: Previous values should be displayed as blank and New values should be displayed as per step 3

Step 5
Description: Navigate to DIS --> Generate Disclosure and change the "Receipt Date:" under Transaction History.
Screen: DIS --> Generate Disclosure
Test Data: nan
Expected Result: "Receipt Date" should be changed.

Step 6
Description: Navigate to DOCS --> Closing Details and verify the "Most Recent LE Receipt Date" is same as "Receipt Date". Again change the Agent Disbursement Date and Recession date by entering a different date.
Screen: DOCS --> Closing Details
Test Data: nan
Expected Result: Dates should be updat

--- RAW DOC 10 ---
TestCaseId: 680177_679185_DTC_03
Content Preview:

TestCase: 680177_679185_DTC_03
Channels: DTC

Pre-Condition & Assumptions:
Originate the loan from Ignite
1. Channel: DTC 
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: "Application Accepted"
6. User should Not have privilege 1301
7. Loan creation date should be such that it fllls within the waiver functionality roll out date.
8. Subject Property Address - 202 S Palmetto St, Rockport, TX, 78382 (Test Data that gives DU Title waiver as None)

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment 
Screen: Login Page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 02
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 03
Description: Navigate to AUS > Request tab and Submit AUS request for DU.
Screen: AUS
Test Data: nan
Expected Result: DU should be run successfully.

Step 04
Description: Navigate to Selection tab and verify the Title Waiver > Waiver Eligibility
Screen: AUS
Test Data: nan
Expected Result: Title Waiver > Waiver Eligibility should be "None"

Step 05
Description: Navigate to 1008 > UW Analysis > AU/DU Findings section
Screen: UW Analysis
Test Data: nan
Expected Result: Title Waiver' field should be present with value as 'None' and with Lock Icon next to it with both fields Disabled and Read-Only.

Step 06
Description: Logout and Login again with Privilege 1301 and Navigate to 1008 > UW Analysis > AU/DU Findings section
Screen: UW Analysis
Test Data: nan
Expected Result: Title Waiver' field should be present with value as 'None' and with Lock Icon next to it.

Step 07
Description: Verify 'Title Waiver' field drop down is enabled and user is able to modify the value from 'None' to 'Eligible'
Screen: UW Analysis
Test Data: nan
Expected Result: Title Waiver' should be changed to 'Eligible' from 'None'.



--- RAW DOC 11 ---
TestCaseId: 661367_DTC_03
Content Preview:

TestCase: 661367_DTC_03
Channels: DTC

Pre-Condition & Assumptions:
Create a new loan in HELOC digital app with the following pre-conditions: 
https://uat-myapp.newrez.com/heloc  
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: HELOC
4. Product Code: NRZHELOC
5. Loan Stage: "UW Submitted"
6. H2O-D Processing and Underwriting Flag should be ‘Y’ 
7. IDUW activity should present on Manger Dashboard and assigned to the right user.
8. Service address - 16321 NW 45th Ave, Opa Locka, FL 33054)
9. User has privilege 1280

=========== TEST STEPS ===========

Step 01
Description: Login to H2O-D as above Underwriter user.
Screen: Login Page
Test Data: https://uat-h2od.caliberhomeloans.com
Expected Result: Login should be successful

Step 02
Description: Navigate to 'Initial Decision Activities Assigned to Me' section and click on loan number.
Screen: Loan Summary
Test Data: Loan number:
Expected Result: IDUW activity should be open for the loan

Step 03
Description: Go to Loan Analysis task step and click on "complete UW analysis"
Screen: nan
Test Data: nan
Expected Result: Loan should be open in middle panel 

Step 04
Description: Navigate to Screens > Pre-Underwriting > Pre-Underwriting main > Expiration Dates and verify "Assets Document Date" and "Assets Document Expiration" field.
Screen: Pre-Underwriting Main
Test Data: nan
Expected Result: • "Assets Document Date" should be available and allow to enter date value.
• "Assets Document Expiration" text field should be displayed with checkbox next to it.
• Checkbox should be editable for privileged user.

Step 05
Description: Click on "Assets Document Date" field and Select current date in date picker. Click on "Save" button.
Screen: Pre-Underwriting Main
Test Data: nan
Expected Result: System should auto calculate "Assets Document Expiration" based on the configuration logic which is "Assets Document Date + 120 days"

Step 06
Description: Click on the date tracker and verify the below item label:
Assets Document E

--- RAW DOC 12 ---
TestCaseId: 573135_DTC_01
Content Preview:

TestCase: 573135_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Document type: Full
5. Loan Stage: "Application Accepted" 
6. Product: CF30





=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful 

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in the new window

Step 04
Description: Verify the Fields in new Modernized Audit Screen
Screen: Modernized Audit
Test Data: nan
Expected Result: The following Fields should be available in the new Modernized Audit screen:
1. Search Frame under Audit 
2. View dropdown with default selection as "Loan" 
3. Search text field labeled as Field 
4. "Date Occurred, From" Field 
5. "Date Occurred, To" Field
6. Search Button
7. Reset Filters Button

Step 05
Description: Click on calendar icon next to "Date Occurred, From"
Screen: Modernized Audit
Test Data: nan
Expected Result: Calendar control should be opened

Step 06
Description: Select a date on the Calendar 

Screen: Modernized Audit
Test Data: nan
Expected Result: Date selected in Calendar should be loaded in "Date Occurred, From" field

Step 07
Description: Click on Search Button
Screen: Modernized Audit
Test Data: nan
Expected Result:  "Date Occurred, From can not be empty while Date Occurred, To is not. Please select both the dates or remove both" UI message should be displayed

Step 08
Description: Click on Ok
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be displayed

Step 09
Description: R

--- RAW DOC 13 ---
TestCaseId: 573135_DTC_03
Content Preview:

TestCase: 573135_DTC_03
Channels: DTC

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: VA
4. Document type: Full
5. Loan Stage: "Application Accepted" 
6. Product: VF30

Note: Changes has been made in the loan to display minimum 60 Audit Logs



=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful 

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened in the new window

Step 04
Description: Verify the Fields in new Modernized Audit Screen
Screen: Modernized Audit
Test Data: nan
Expected Result: The following Fields should be available in the new Modernized Audit screen:
1. Search Frame under Audit 
2. View dropdown with default selection as "Loan" 
3. Search text field labeled as Field 
4. "Date Occurred, From" Field 
5. "Date Occurred, To" Field
6. Search Button
7. Reset Filters Button

Step 05
Description: Verify the Audit Logs
Screen: Modernized Audit
Test Data: nan
Expected Result: 50 loan level audited entries should be displayed in Audit logs by default

Note: For loan having less than 50 loan level audit records, only those number of records should be displayed.  

Step 06
Description: Verify Records per page dropdown at footer 
Screen: Modernized Audit
Test Data: nan
Expected Result: Records per page dropdown at footer should be defaulted with value of 50  

Step 07
Description: Change per page dropdown selection for Records per page and verify dropdown changes 
Screen: Modernized Audit
Test Data: nan
Expected Result: Screen size should load records based on the

--- RAW DOC 14 ---
TestCaseId: 565797_DTC_01
Content Preview:

TestCase: 565797_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
Create two new loans in Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: "Application Accepted"

Note: Testing should be done in Edge browser

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 environment.
Screen: https://qch2o.caliberdirect.com
Test Data: nan
Expected Result: Login should be successful.

Step 02
Description: Open the first loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened fort the first loan.

Step 03
Description: Navigate to Tools->Modernized Audit.
Screen: Audit
Test Data: nan
Expected Result: User should be navigated to "Modernized Audit" screen.

Step 04
Description: Don't close the "Modernized Audit" screen and navigate back to "Loan Summary" screen of the first loan.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened for the first loan.

Step 05
Description: Enter the second loan number in the 
"Search" box and click on "Go" button.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened for the second loan.

Step 06
Description: Navigate back to unclosed "Modernized 
Audit" screen of the first loan.
Screen: Audit
Test Data: nan
Expected Result: System should display a warning pop up with verbiage "Loan has changed. Click 'Ok' to refresh." and the pop up should also display "Ok" button.

Step 07
Description: Click "Ok" button on the pop up.
Screen: Audit
Test Data: nan
Expected Result: User should be able to verify the following:
• System should sync "Modernized Audit" 
screen to current loan (second loan).
• "Modernized Audit" screen should reset to its default state.
• "Select" should be selected as default value in "View" dropdown.
• Loan number field should reflect the current (second) loa

--- RAW DOC 15 ---
TestCaseId: 640992_DTC_01
Content Preview:

TestCase: 640992_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
Loan should be created with below pre-conditions:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: FHA
4. Product Code: Any
5. Loan stage should be "Application Accepted"
6. Brand: Any JV



=========== TEST STEPS ===========

Step 1
Description: Login to H2O-A in UAT environment.
Screen: H2O A Login page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 2
Description: Open loan which is created as per the preconditions  
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary page should be opened.

Step 3
Description: Navigate to 1008>ATR/QM screen>Under ATR/QM section and values are selected for below fields.
◦ ATR Type
◦ QM Safe Harbor Status
◦ QM Type
◦ Transaction Exempt From ATR Requirements
◦ Exempt HPML Status
◦ ATR Exempt Transaction Type
◦ Loan SubjectTo Forward Commitment
◦Loan Transferred After Consummation
◦ Credit Purpose
Note: If values are not present for any fields then select the value from dropdown.
Screen: ATR/QM
Test Data: nan
Expected Result: Values should be present for ATR/QM section fields.

Step 4
Description: Navigate to Tools>Modernized Audit screen>Select below new fields from Field and click on Search.
◦ ATR Type
◦ QM Safe Harbor Status
◦ QM Type
◦ Transaction Exempt From ATR Requirements
◦ Exempt HPML Status
◦ ATR Exempt Transaction Type
◦ Loan SubjectTo Forward Commitment
◦Loan Transferred After Consummation
◦ Credit Purpose
Screen: Modernized Audit 
Test Data: nan
Expected Result: All the selected records should be displayed and Previous Value should be 'Blank' and New Value should be the selected values as per step 3.

Step 5
Description: Close the Modernized Audit and navigate to ATR/QM screen and select the another values for ATR/QM all fields and click on Save.
Screen: ATR/QM
Test Data: nan
Expected Result: New values should be selected and saved.

Step 6
Description: Navigate to Tools>Modernized Audi

--- RAW DOC 16 ---
TestCaseId: 680177_679185_DTC_01
Content Preview:

TestCase: 680177_679185_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
Originate the loan from H2O-A
1. Channel: DTC 
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: "Application Accepted"
6. User should have privilege 1301
7. AUS should not be run yet.
8. Loan creation date should be such that it fllls within the waiver functionality roll out date.
9.  Subject Property Address: 3431 Fort Ave, Eureka, CA, 95503  - Test Address for DU Title Waiver resulting as Eligible.

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment 
Screen: Login Page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 02
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 03
Description: Navigate to AUS > History Tab
Screen: AUS
Test Data: nan
Expected Result: No Records should be present.

Step 04
Description: Navigate to 1008 > UW Analysis > AU/DU Findings section
Screen: UW Analysis
Test Data: nan
Expected Result: Title Waiver' field should be present with Lock Icon next to it and Disabled.

Step 05
Description: Navigate to AUS > Request tab and Submit AUS request for DU
Screen: AUS
Test Data: nan
Expected Result: DU should be run successfully.

Step 06
Description: Navigate to Selection tab and verify the Title Waiver > Waiver Eligibility
Screen: AUS
Test Data: nan
Expected Result: Title Waiver > Waiver Eligibility should be "Eligible"

Step 07
Description: Navigate to 1008 > UW Analysis > AU/DU Findings section
Screen: UW Analysis
Test Data: nan
Expected Result: Title Waiver' field should be present with value as 'Eligible' and with Lock Icon next to it.

Step 08
Description: Verify 'Title Waiver' field drop down is enabled and user is able to modify the value from 'Eligible' to 'None'
Screen: UW Analysis
Test Data: nan
Expected Res

--- RAW DOC 17 ---
TestCaseId: 623485_623489_DTC_01
Content Preview:

TestCase: 623485_623489_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
1. Channel: DTC.
2. Loan Purpose: Refinance.
3. Loan Type: VA.
4. Product: VF30.
5. Loan Stage: "UW Submitted".

User should have privilege 1280.
Loan should be created after Nov release deployment.

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT environment.
Screen: Dashboard
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened.

Step 03
Description: Navigate to Screens > Pre-Underwriting > Pre-Underwriting main > Expiration Dates and verify the fields.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Below new fields should be displayed under Expiration Dates section.
• Income Document Date
• Assets Document Date

Step 04
Description: Click on the text box for "Income Document Date" and "Asset Document Date" field.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Calendar should be opened.

Step 05
Description: Select the dates for each field and click on save.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Dates should be selected for each field.

Step 06
Description: Navigate to Tools > Modernized Audit and verify the 'Income Document Expiration' & 'Assets Document Date' record
Screen: Modernized Audit
Test Data: nan
Expected Result: Records should be display pervious value and new value correctly.

Step 07
Description: Close the Mod Audit and Navigate to Tools >  Audit and verify the 'Income Document Expiration'& 'Assets Document Date' record
Screen: Audit pop up
Test Data: nan
Expected Result: Records should be display pervious value and new value correctly.

Step 08
Description: Navigate to 1008 > UW analysis > Expiration Dates and verify below fields.
• Income Document Date
• Assets Document Date
Screen: UW an

--- RAW DOC 18 ---
TestCaseId: 661367_DTC_01
Content Preview:

TestCase: 661367_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
Originate the loan from Ignite portal with following pre-conditions:
https://uat-ignite.caliberhomeloans.com/

1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: HELOC
4. Product Code: NRZHELOC
5. Loan Stage: "Application Accepted"
6. "Assets Document Date" should be blank.
7. User should have privilege 1280

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 Environment
Screen: Login Page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 02
Description: Open the Loan created as per preconditions
Screen: Loan Summary
Test Data: Loan number:
Expected Result: Loan summary screen should be opened

Step 03
Description: Navigate to Screens > Pre-Underwriting > Pre-Underwriting main > Expiration Dates section and verify "Assets Document Date" and "Assets Document Expiration" fields.
Screen:  Pre-Underwriting Main
Test Data: nan
Expected Result: • "Assets Document Date" should be available and should allow  user to enter date value.
• "Assets Document Expiration" text field should be displayed with checkbox next to it without any date.
• Checkbox should be editable for privileged user.

Step 04
Description: Click on "Assets Document Date" field and Select current date in date picker. Click on "Save" button.
Screen:  Pre-Underwriting Main
Test Data: nan
Expected Result: System should auto calculate "Assets Document Expiration" based on the configuration logic which is "Assets Document Date + 120 days"

Step 05
Description: Navigate to Tools > Modernized Audit and verify the 'Income Document Expiration' & 'Assets Document Date' record
Screen: Modernized Audit
Test Data: nan
Expected Result: •  Previous value should be blank and New value should be the date of the entered value in step 4 for "Assets Expiration"
• Previous value should be blank and New value should be "False" for  "Asset Document Expiration Not Required " 

Step 06

--- RAW DOC 19 ---
TestCaseId: 659272_659276_DTC_02
Content Preview:

TestCase: 659272_659276_DTC_02
Channels: DTC

Pre-Condition & Assumptions:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type: Conv
4. Product:   JF30B (GLACIER)
5. AUS successfully run
6. Loan Stage: "Application Submitted"

User should have privilege 1280
Loan should be created after Jan release deployment.

=========== TEST STEPS ===========

Step 01
Description: Login to H2O in UAT environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com/
Expected Result: Login should be successful

Step 02
Description: Open the loan as per pre-condition
Screen: nan
Test Data: nan
Expected Result: Loan summary screen should be displayed.

Step 03
Description: Navigate to Screen --> Pre-Underwriting --> Pre-Underwriting Main --> Expiration Dates and verify the "Credit Report Expiration" field.
Screen: Pre-Underwriting Main
Test Data: nan
Expected Result: Credit Report Expiration" date should be auto populate as 'Credit Report Order Date + 90 days' and Credit Report Expiration checkbox should be unchecked

Step 04
Description: Navigate to 1008 --> UW analysis --> Expiration Dates and verify the "Credit Report Order" date and "Credit Report Expiration" field.
Screen: 1008 > UW analysis
Test Data: nan
Expected Result: • "Credit Report Order" date should be displayed.
• Credit Report Expiration" date should be auto populate as 'Credit Report Order Date + 90 days' and Credit Report Expiration checkbox should be unchecked

Step 05
Description: Remove the  Credit Report Order Date and Observe the "Credit Report Expiration" text field
Screen: 1008 > UW analysis
Test Data: nan
Expected Result: • Credit Report Expiration checkbox should be unchecked and enabled.
• Credit Report Expiration text field should be clear and disabled.

Step 06
Description: Logout and login with user who doesn't have privilege 1280
Screen: nan
Test Data: https://qch2o.caliberdirect.com/
Expected Result: Login should be successful

Step 07
Description: Navigate to Screen --> Pre-Underwriting --> Pr

--- RAW DOC 20 ---
TestCaseId: 658373_DTC_01
Content Preview:

TestCase: 658373_DTC_01
Channels: DTC

Pre-Condition & Assumptions:
Create a new loan with the following pre-conditions from HELOC App:
1. Channel: DTC
2. Loan Purpose: Refianace
3. Loan Type: HELOC
4. Loan Product: Any




=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 Environment
Screen: Login Page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 02
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 03
Description: Navigate to 1008 > Appraisal & Property screen
Screen: Appraisal & Property screen
Test Data: nan
Expected Result: Appraisal & Property screen should be opened.

Step 04
Description: Verify the below new fields under "Project Information section":
• 'CPM Certification ID #'
• 'Freddie PAR #'
• 'PWR Number'
Screen: Appraisal & Property screen
Test Data: nan
Expected Result: Below new fields should be visible under "Project Information section":
• 'CPM Certification ID #'   (underneath 'CPM Project ID #' field)
• 'Freddie PAR #'                 (underneath the new 'CPM Certification ID #' field)
• 'PWR Number'                  (underneath 'Target Agency Info' dropdown)

Step 05
Description: Enter Special characters, alphabets & spaces in the new field 'CPM Certification ID #' 
Screen: Appraisal & Property screen
Test Data: nan
Expected Result: New field 'CPM Certification ID #' should not allow user to enter Special characters, alphabets and spaces.

Step 06
Description: Enter numeric characters in the new field 'CPM Certification ID #' 
Screen: Appraisal & Property screen
Test Data: nan
Expected Result: New field 'CPM Certification ID #' should allow user to enter numeric characters and maximum 10 characters.

Step 07
Description: Enter Special characters, alphabets & spaces in the new field 'Freddie PAR #' 
Screen: Appraisal & Property screen
Tes

--- RERANKED ORDER ---
Rank 1: 564936_564981_DTC_01
Rank 2: 579578_DTC_01
Rank 3: 573135_DTC_02
Rank 4: 573135_DTC_01
Rank 5: 573135_DTC_03
Rank 6: 565797_DTC_01
Rank 7: 738152_DTC_01
Rank 8: 718524_DTC_01
Rank 9: 647338_647343_DTC_Pipeline_01
Rank 10: 680177_679185_DTC_02
Rank 11: 673197_670270_665820_DTC_01
Rank 12: 718516_DTC_01
Rank 13: 680177_679185_DTC_03
Rank 14: 661367_DTC_03
Rank 15: 640992_DTC_01
Rank 16: 680177_679185_DTC_01
Rank 17: 623485_623489_DTC_01
Rank 18: 661367_DTC_01
Rank 19: 659272_659276_DTC_02
Rank 20: 658373_DTC_01

--- STRUCTURED EXTRACTION ---
{
  "scenario": "564936_564981_DTC_01",
  "script": "Channels: DTC",
  "precondition": "Create a new loan in Mismo 3.4 XML file. Pre-Conditions: 1. Channel: RTL 2. Loan Purpose: Purchase 3. Loan Type: Conventional 4. Product Code: CF30 5. Loan Stage: 'Application Accepted' 6. No. of Application: 1 7. No. of Borrowers in 1st Application: 1 (e.g., 1st application has Alice Firstimer)",
  "steps": [
    {
      "stepNo": "01",
      "description": "Log in to H2O-A in UAT1 environment.",
      "expectedResult": "Login should be successful."
    },
    {
      "stepNo": "02",
      "description": "Open the loan which is created as per the Pre-Conditions.",
      "expectedResult": "Loan summary screen should be opened."
    },
    {
      "stepNo": "03",
      "description": "Navigate to Tools->Modernized Audit.",
      "expectedResult": "'Search' button should not be present in 'Modernized Audit' screen."
    },
    {
      "stepNo": "04",
      "description": "Verify default selection value of 'View' dropdown.",
      "expectedResult": "'Select' should be selected as default in 'View' dropdown."
    },
    {
      "stepNo": "05",
      "description": "Click on 'View' dropdown.",
      "expectedResult": "User should verify that '(P)' is displayed before primary borrower of 1st application and 'View' dropdown should display following options in the order: \u2022 Select \u2022 Loan \u2022 (P) Name of Primary Borrower of 1st Application"
    },
    {
      "stepNo": "06",
      "description": "Select 'Loan' from 'View' dropdown.",
      "expectedResult": "User should be able to select 'Loan' from 'View' dropdown."
    },
    {
      "stepNo": "07",
      "description": "Verify audit logs.",
      "expectedResult": "Audit logs should not be displayed until user clicks on 'Search' field."
    },
    {
      "stepNo": "08",
      "description": "Click on 'Search' field.",
      "expectedResult": "'Search' field should display all loan level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "09",
      "description": "Verify all loan level audit field logs.",
      "expectedResult": "User should be able to verify all loan level audit field logs."
    },
    {
      "stepNo": "10",
      "description": "Enter 'ap' in the search field.",
      "expectedResult": "System should not filter audit fields list matching the characters 'ap'."
    },
    {
      "stepNo": "11",
      "description": "Enter 'appr' in the search field.",
      "expectedResult": "System should filter audit fields list matching the characters 'appr'."
    },
    {
      "stepNo": "12",
      "description": "Enter 'Type of Property' in the search field and select 'Type of Property'.",
      "expectedResult": "\u2022 User should verify that space is also counted as a character. \u2022 System should populate all the records of the selected field 'Type of Property'."
    },
    {
      "stepNo": "13",
      "description": "Select primary borrower of 1st application from 'View' dropdown.",
      "expectedResult": "User should be able to select primary borrower of 1st application from 'View' dropdown."
    },
    {
      "stepNo": "14",
      "description": "Verify audit logs.",
      "expectedResult": "Audit logs should not be displayed until user clicks on 'Search' field."
    },
    {
      "stepNo": "15",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all primary borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "16",
      "description": "Logout from H2OA.",
      "expectedResult": "Log out should be successful."
    }
  ]
}

--- FINAL SELECTED STRUCTURED DOC ---
{
  "scenario": "564936_564981_DTC_01",
  "script": "Channels: DTC",
  "precondition": "Create a new loan in Mismo 3.4 XML file. Pre-Conditions: 1. Channel: RTL 2. Loan Purpose: Purchase 3. Loan Type: Conventional 4. Product Code: CF30 5. Loan Stage: 'Application Accepted' 6. No. of Application: 1 7. No. of Borrowers in 1st Application: 1 (e.g., 1st application has Alice Firstimer)",
  "steps": [
    {
      "stepNo": "01",
      "description": "Log in to H2O-A in UAT1 environment.",
      "expectedResult": "Login should be successful."
    },
    {
      "stepNo": "02",
      "description": "Open the loan which is created as per the Pre-Conditions.",
      "expectedResult": "Loan summary screen should be opened."
    },
    {
      "stepNo": "03",
      "description": "Navigate to Tools->Modernized Audit.",
      "expectedResult": "'Search' button should not be present in 'Modernized Audit' screen."
    },
    {
      "stepNo": "04",
      "description": "Verify default selection value of 'View' dropdown.",
      "expectedResult": "'Select' should be selected as default in 'View' dropdown."
    },
    {
      "stepNo": "05",
      "description": "Click on 'View' dropdown.",
      "expectedResult": "User should verify that '(P)' is displayed before primary borrower of 1st application and 'View' dropdown should display following options in the order: \u2022 Select \u2022 Loan \u2022 (P) Name of Primary Borrower of 1st Application"
    },
    {
      "stepNo": "06",
      "description": "Select 'Loan' from 'View' dropdown.",
      "expectedResult": "User should be able to select 'Loan' from 'View' dropdown."
    },
    {
      "stepNo": "07",
      "description": "Verify audit logs.",
      "expectedResult": "Audit logs should not be displayed until user clicks on 'Search' field."
    },
    {
      "stepNo": "08",
      "description": "Click on 'Search' field.",
      "expectedResult": "'Search' field should display all loan level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "09",
      "description": "Verify all loan level audit field logs.",
      "expectedResult": "User should be able to verify all loan level audit field logs."
    },
    {
      "stepNo": "10",
      "description": "Enter 'ap' in the search field.",
      "expectedResult": "System should not filter audit fields list matching the characters 'ap'."
    },
    {
      "stepNo": "11",
      "description": "Enter 'appr' in the search field.",
      "expectedResult": "System should filter audit fields list matching the characters 'appr'."
    },
    {
      "stepNo": "12",
      "description": "Enter 'Type of Property' in the search field and select 'Type of Property'.",
      "expectedResult": "\u2022 User should verify that space is also counted as a character. \u2022 System should populate all the records of the selected field 'Type of Property'."
    },
    {
      "stepNo": "13",
      "description": "Select primary borrower of 1st application from 'View' dropdown.",
      "expectedResult": "User should be able to select primary borrower of 1st application from 'View' dropdown."
    },
    {
      "stepNo": "14",
      "description": "Verify audit logs.",
      "expectedResult": "Audit logs should not be displayed until user clicks on 'Search' field."
    },
    {
      "stepNo": "15",
      "description": "Click on 'Search' field and verify all borrower level audit logs.",
      "expectedResult": "'Search' field should display all primary borrower level audit fields list sorted in alphabetical order(A-Z)."
    },
    {
      "stepNo": "16",
      "description": "Logout from H2OA.",
      "expectedResult": "Log out should be successful."
    }
  ]
}

====================================
CHANNEL: CL1
====================================

Retrieved 20 documents


--- RAW DOC 1 ---
TestCaseId: 738152_CL1_01
Content Preview:

TestCase: 738152_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create a new loan from Blue Print with the following pre-condition:
1. Channel: CL1
2. Loan Purpose: Refinance
3. Loan Type: VA
4. Product: VF30 (Lender Paid)
5. Loan Stage: Created


=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment 
Screen: Login Page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 2
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to Electronic Delivery and Borrower Auth & Cert and verify the "Enable Electronic Disclosures" checkbox
Screen: Loan Summary
Test Data: nan
Expected Result: "Enable Electronic Disclosures" checkbox should be unchecked

Step 4
Description: Check the "Enable Electronic Disclosures" checkbox
Screen: Generate DOCS
Test Data: nan
Expected Result: "Enable Electronic Disclosures" checkbox should be checked

Step 5
Description: Navigate to Tools > Modernized Audit and verify the entry created for the field name "Electronic Consent":
- User who made the change 
- Time Occurred 
- Previous Value 
- New Value 
- Field name as "Electronic Consent"

Screen: Modernized Audit 
Test Data: nan
Expected Result: Record should be created for the field "Electronic Consent" and previous value should be false and new value should be true


Step 6
Description: Uncheck the "Enable Electronic Disclosures" checkbox
Screen: Loan Summary
Test Data: nan
Expected Result: "Enable Electronic Disclosures" checkbox should be unchecked

Step 7
Description: Navigate to Tools > Modernized Audit and verify the entry created for the field name "Electronic Consent":
- User who made the change 
- Time Occurred 
- Previous Value 
- New Value 
- Field name as "Electronic Consent"

Screen: Modernized Audit 
Test Data: nan
Expected Result: Record should be create

--- RAW DOC 2 ---
TestCaseId: 718523_CL1_01
Content Preview:

TestCase: 718523_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create a loan from BP:
1. Channel: CL1
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: Any
5. Loan stage should be UW Submitted
6. Early Disclosure should be generated, sent via eSign ,Esign should be completed and disclosure should be received. 

=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment
Screen: Login Page
Test Data: https://uath2o.newrez.com
Expected Result: Login should be successful

Step 2
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Navigate to DOCS --> Closing Details and check the below dates:
• Earliest Consummation Date
• Most Recent LE Receipt Date
• Agent Disbursement Date
Note: Enter some date in Agent Disbursement date if date is not present.
Screen: DOCS --> Closing Details
Test Data: nan
Expected Result: Mentioned dates should be present under DOCS --> Closing Details.

Step 4
Description: Navigate to Tools --> Modernized Audit and verify the below field values:
• Earliest Consummation Date
• Most Recent LE Receipt Date
• Agent Disbursement Date
Screen: Modernized Audit
Test Data: nan
Expected Result: Values should be present in the form of Previous Value and New Value and same as DOCS --> Closing Details.

Step 5
Description: Navigate to DIS --> Generate Disclosure and change the "Receipt Date:" under Transaction History.
Screen: DIS --> Generate Disclosure
Test Data: nan
Expected Result: "Receipt Date" should be changed.

Step 6
Description: Navigate to DOCS --> Closing Details and verify the "Most Recent LE Receipt Date" is same as "Receipt Date". Again change the Agent Disbursement Date by entering a different date.
Screen: DOCS --> Closing Details
Test Data: nan
Expected Result: Dates should be updated.

Step 7
Description: Navigate to Tools --> Modernized A

--- RAW DOC 3 ---
TestCaseId: 673197_670270_665820_CL1_01
Content Preview:

TestCase: 673197_670270_665820_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create a new loan in H2O-A with following pre-conditions.
1. Channel: CL1
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product Code: Any
5. Loan Stage: "UW Submitted"
6. H2O-D Processing and Underwriting Flag should be ‘Y’ 
7. IDUW activity should present on Manger DB and assigned to user.

=========== TEST STEPS ===========

Step 01
Description: Login to H2O-D as above Underwriter user without privilege 1298
Screen: Login page
Test Data: https://uat-h2od.caliberhomeloans.com
Expected Result: Login should be successful

Step 02
Description: Navigate to 'Initial Decision Activities Assigned to Me' section > click on loan number > Go to step Credit and click on "Verify Loan Details"
Screen: Dashboard
Test Data: nan
Expected Result: Loan summary page should be opened in Middle panel.

Step 03
Description: Verify the loan header.

Note: Verify this step on all the screens (all 1003, 1008, DIS, DOCS, All the Left Menu screen, Brown screen)
Screen: Loan Summary
Test Data: nan
Expected Result: • "Servicing Fraud Alert" field along with the checkbox should be available on the loan header.
• "Servicing Fraud Alert" checkbox should be disabled.

Step 04
Description: Login to H2O-D as above Underwriter user with privilege 1298
Screen: Login page
Test Data: https://uat-h2od.caliberhomeloans.com
Expected Result: Login should be successful

Step 05
Description: Navigate to 'Initial Decision Activities Assigned to Me' section > click on loan number > Go to step Credit and click on "Verify Loan Details"
Screen: Dashboard
Test Data: nan
Expected Result: Loan summary page should be opened in Middle panel.

Step 06
Description: Verify the loan header.

Note: Verify this step on all the screens (all 1003, 1008, DIS, DOCS, All the Left Menu screen, Brown screen)
Screen: Loan Summary
Test Data: nan
Expected Result: • "Servicing Fraud Alert" field along with the checkbox should be available on t

--- RAW DOC 4 ---
TestCaseId: 749011_CL1_01
Content Preview:

TestCase: 749011_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create a new loan from Broker Portal according to the following preconditions:
1. Channel: CL1
2. Loan Purpose: Purchase
3. Type: FHA
4. Product: FF30
5. Property Type: Attached.
6. Loan Stage: Created. 

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT environment. 
Screen: Login Page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful. 

Step 02
Description: Open the loan which is created as per the preconditions.
Screen: Loan Summary 
Test Data: nan
Expected Result: Loan Summary screen should be opened.

Step 03
Description: Navigate to Status > FHA - Request Case Number > Type of Case
Screen: FHA Case  Number Request
Test Data: nan
Expected Result: FHA Case  Number Request screen should be opened.

Step 04
Description: Verify the "Is the Property a Condo" and "Is property a HUD 184 option?” field. 
Screen: FHA Case  Number Request
Test Data: nan
Expected Result: • "Is the Property a Condo" field should be present
• "Is property a “HUD 184 option”?" field should be present below the "Is the Property a Condo"
• Yes and No radio buttons should be present for both the fields with default value selected to "No"


Step 05
Description: Verify that the field "Are you requesting a Single Unit Approval (SUA) Condo?"
Screen: FHA Case  Number Request
Test Data: nan
Expected Result: The field "Are you requesting a Single Unit Approval (SUA) Condo?" should not be present.

Step 06
Description: Verify if the field "Is property a Site Condo?" is present or not.
Screen: FHA Case  Number Request
Test Data: nan
Expected Result: The  "Is property a Site Condo?" field should not be present.

Step 07
Description: Navigate to 1003 > Loan Summary > Property Details. Update the property type to "Detached Condominium" and save it.
Screen: Loan Summary 
Test Data: nan
Expected Result: The property type should be updated and saved successfully. 

Step 08
Descrip

--- RAW DOC 5 ---
TestCaseId: 647338_647343_CL1_01
Content Preview:

TestCase: 647338_647343_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create the Loan from Customer Portal:
2. Loan channel: CL1
3. Ops Center: Any
4. Loan Type - Conventional
5. Loan Purpose - Purchase
6. Loan Product - CF30
7. H2O-D Processing and UW Flag should be ‘Y’.                     
8. Loan Stage - "Created".
9. User should have privilege 1016.


=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 Environment.
Screen: Login Page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Log in should be successful.

Step 02
Description: Open the Loan created as per preconditions
Screen: Loan Summary
Test Data: Loan number:
Expected Result: Loan summary screen should be opened

Step 03
Description: Navigate to Screens-> Government Insuring > Insurance Tracking screen and verify the fields.
Screen:  Screens-> Government Insuring > Insurance Tracking
Test Data: nan
Expected Result: Below fields should be present:
• ‘Government insuring received date’ label is renamed to ‘Government Audit Received Date’.
• A new 'date picker' field with label ‘Post-Closing Reviewed Date’ should be present under ‘Agency Case Number’ field.
• ‘Government insuring review date’ label is renamed to ‘Government Insuring Review Date’.

Step 04
Description: Verify the Below fields:
• Government Audit Received Date
• Post-Closing Reviewed Date
• Government Insuring Review Date
Screen:  Screens-> Government Insuring > Insurance Tracking
Test Data: nan
Expected Result: All the fields should be disabled.

Step 05
Description: Assign privilege 1016 to the IDUW user
Screen: nan
Test Data: nan
Expected Result: Privilege should be assigned.

Step 06
Description: Navigate to Screens-> Government Insuring > Insurance Tracking screen and Verify the Below fields:
• Government Audit Received Date
• Post-Closing Reviewed Date
• Government Insuring Review Date
Screen:  Screens-> Government Insuring > Insurance Tracking
Test Data: nan
Expected Result: All the field

--- RAW DOC 6 ---
TestCaseId: 742390_CL1_01
Content Preview:

TestCase: 742390_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create a new loan from Broker Portal with below pre-conditions:
1. Channel: CL1
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Product: CF30
5. Loan Stage: Created


=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment 
Screen: Login Page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 2
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Go to Admin>Client Admin>Client Search and search with the Broker name you created the loan with
Screen: Client Admin
Test Data: nan
Expected Result: Broker Admin page should be displayed.

Step 4
Description: Navigate to Company Tab -> Newrez Section and verify the "Customer Protection Level" field.
Screen: Broker Admin
Test Data: nan
Expected Result: "Customer Protection Level"  should be null field should be null.

Step 5
Description: Request the BP team to update the "IsRezClub = False" and "IsProtection = True" 
Screen: nan
Test Data: nan
Expected Result: The following fields should display as "IsRezClub = False" and "IsProtection = True"

Step 6
Description: Navigate to Company Tab >> Newrez Section and verify the "Customer Protection Level" field.
Screen: Broker Admin
Test Data: nan
Expected Result: "Customer Protection Level"  value should be changed to "18 Months".

Step 7
Description: Navigate to Audit Trail tab ->Translog -> Company Translog section.
Screen: Broker Admin
Test Data: nan
Expected Result: Audit trail tab should display

Step 8
Description:  verify that audit entry is created capturing below:
- User who made the change  
- Time Occurred  
- Previous Value  
- New Value 
- Field name as "DCalcCustomerProtectionLevel" 
Screen: Audit Trail
Test Data: nan
Expected Result: Audit entry should be created capturing bel

--- RAW DOC 7 ---
TestCaseId: 623485_623489_CL1_01
Content Preview:

TestCase: 623485_623489_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
1. Channel: CL1.
2. Loan Purpose: Purchase.
3. Loan Type: Conventional.
4. Product: CF30.
5. Loan Stage: "UW Submitted".

User should have privilege 1280.
Loan should be created after Nov release deployment.

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT environment.
Screen: Dashboard
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful.

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan summary screen should be opened.

Step 03
Description: Navigate to Screens > Pre-Underwriting > Pre-Underwriting main > Expiration Dates and verify the fields.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Below new fields should be displayed under Expiration Dates section.
• Income Document Date
• Assets Document Date

Step 04
Description: Click on the text box for "Income Document Date" and "Asset Document Date" field.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Calendar should be opened.

Step 05
Description: Select the dates for each field and click on save.
Screen: Pre-Underwriting main
Test Data: nan
Expected Result: Dates should be selected for each field.

Step 06
Description: Navigate to Tools > Modernized Audit and verify the 'Income Document Expiration' & 'Assets Document Date' record
Screen: Modernized Audit
Test Data: nan
Expected Result: Records should be display pervious value and new value correctly.

Step 07
Description: Close the Mod Audit and Navigate to Tools >  Audit and verify the 'Income Document Expiration'& 'Assets Document Date' record
Screen: Audit pop up
Test Data: nan
Expected Result: Records should be display pervious value and new value correctly.

Step 08
Description: Navigate to 1008 > UW analysis > Expiration Dates and verify below fields.
• Income Document Date
• Assets Document Date
Scre

--- RAW DOC 8 ---
TestCaseId: 737483_CL1_01
Content Preview:

TestCase: 737483_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create a new loan from Broker Portal according to the following preconditions:
1. Channel: CL1
2. Loan Purpose: Purchase
3. Type: FHA
4. Product: FF30
5. Property Type: Detached.
6. Loan Stage: Created. 

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT environment. 
Screen: Login Page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful. 

Step 02
Description: Open the loan which is created as per the preconditions.
Screen: Loan Summary 
Test Data: nan
Expected Result: Loan Summary should be successful.

Step 03
Description: Navigate to Status > FHA - Request Case Number > Type of Case
Screen: FHA Case  Number Request
Test Data: nan
Expected Result: FHA Case  Number Request screen should be opened.

Step 04
Description: Verify the "Is the Property a Condo" and "Is property a HUD 184 option”? field. 
Screen: FHA Case  Number Request
Test Data: nan
Expected Result: • "Is the Property a Condo" field should be present
• "Is property a “HUD 184 option”?" field should be present below the "Is the Property a Condo"
• Yes and No radio buttons should be present for both the fields with default value selected to "No"


Step 05
Description: Verify that the field "Are you requesting a Single Unit Approval (SUA) Condo?"
Screen: FHA Case  Number Request
Test Data: nan
Expected Result: The field "Are you requesting a Single Unit Approval (SUA) Condo?" should not be present.

Step 06
Description: Verify if the field "Is property a Site Condo?" is present or not.
Screen: FHA Case  Number Request
Test Data: nan
Expected Result: The  "Is property a Site Condo?" field should not be present.

Step 07
Description: Navigate to 1003 > Loan Summary > Property Details. Update the property type to "Detached Condominium" and save it.
Screen: Loan Summary 
Test Data: nan
Expected Result: The property type should be updated and saved successfully. 

Step 08
Descriptio

--- RAW DOC 9 ---
TestCaseId: 718524_CL1_01
Content Preview:

TestCase: 718524_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create Loan Manually with below pre-conditions.
1. Channel: CL1
2. Loan Purpose: Purchase
3. Loan Type: Any
4. Product: Any
5. Loan stage: Created

=========== TEST STEPS ===========

Step 01
Description: Log in to UAT1 H2O-A
Screen: Login page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 02
Description: Open a Loan which is created as per the preconditions.
Screen: Dashboard
Test Data: nan
Expected Result: Loan should be opened and Loan summary page should be opened.

Step 03
Description: Navigate to DIS > LE Fees screen and enter the rate information value and click on master Save.
Screen: nan
Test Data: nan
Expected Result: Rate information value should be saved.

Step 04
Description: Navigate to Audit and Search field YSPPointsAtNoLock in search box
Screen: Audit
Test Data: nan
Expected Result: YSPPointsAtNoLock should be present as Field name along with User, Time Occurred, Previous Value, New Value and New value should match the percent value entered in Rate information.

Step 05
Description: Navigate to Audit and Search field YSPPointsAtNoLock in search box
Screen: Audit
Test Data: nan
Expected Result: YSPPointsAtNoLock should be present as Field name along with User, Time Occurred, Previous Value, New Value and New value should match the percent value entered in Rate information.

Step 06
Description: Search field YSPPointsAtNoLock in search box ApplicationTakenByMediumID. 
Screen: Audit
Test Data: nan
Expected Result: ApplicationTakenByMediumID should not be present.

Step 07
Description: Navigate to DIS > Compliance Check and Generate Compliance check.
Screen: Compliance Check
Test Data: nan
Expected Result: Compliance Check should be run without Error.

Step 08
Description: Navigate to 1003 > Loan Summary and Stage the loan to Final Approval in Review
Screen: Loan Summary
Test Data: nan
Expected Result: Loan stage should be changed to Final Appr

--- RAW DOC 10 ---
TestCaseId: 658373_CL1_01
Content Preview:

TestCase: 658373_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create a new loan with the following pre-conditions from BP:
1. Channel: CL1
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Loan Product: Any (CF30, CF20,...)




=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 Environment
Screen: Login Page
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful

Step 02
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 03
Description: Navigate to 1008 > Appraisal & Property screen
Screen: Appraisal & Property screen
Test Data: nan
Expected Result: Appraisal & Property screen should be opened.

Step 04
Description: Verify the below new fields under "Project Information section":
• 'CPM Certification ID #'
• 'Freddie PAR #'
• 'PWR Number'
Screen: Appraisal & Property screen
Test Data: nan
Expected Result: Below new fields should be visible under "Project Information section":
• 'CPM Certification ID #'   (underneath 'CPM Project ID #' field)
• 'Freddie PAR #'                 (underneath the new 'CPM Certification ID #' field)
• 'PWR Number'                  (underneath 'Target Agency Info' dropdown)

Step 05
Description: Enter Special characters, alphabets & spaces in the new field 'CPM Certification ID #' 
Screen: Appraisal & Property screen
Test Data: nan
Expected Result: New field 'CPM Certification ID #' should not allow user to enter Special characters, alphabets and spaces.

Step 06
Description: Enter numeric characters in the new field 'CPM Certification ID #' 
Screen: Appraisal & Property screen
Test Data: nan
Expected Result: New field 'CPM Certification ID #' should allow user to enter numeric characters and maximum 10 characters.

Step 07
Description: Enter Special characters, alphabets & spaces in the new field 'Freddie PAR #' 
Screen: Appraisal & Pro

--- RAW DOC 11 ---
TestCaseId: 745949_CL1_02
Content Preview:

TestCase: 745949_CL1_02
Channels: CL1

Pre-Condition & Assumptions:
1. CL1 Loan should be created from Blueprint with a valid Broker user id.
2. Product: Any NON-QM Product 
3. IsNonQm should be True under Audit.

NOTE: Select different broker ID from other scripts- example- Sid.joejones

=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment 
Screen: Login Page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 2
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Go to Admin>Client Admin>Client Search and search with the Broker name you created the loan with.
Screen: Client Admin
Test Data: nan
Expected Result: Broker Admin page should be displayed.

Step 4
Description: Click on Audit Trial and verify the field "IsProtected"
Screen: Broker Admin
Test Data: nan
Expected Result:  IsProtected should be True in New column.

Step 5
Description: Click on the Loan tab from the top menu.
Screen: Loan Summary
Test Data: nan
Expected Result: "Customer Protection Level" should be "No Protection" and it should be read only
Note: Check this when RezClub is set to True and set to False

Step 6
Description: Request the BP team to update "IsProtected = False" 
Screen: Broker Admin
Test Data: nan
Expected Result: BP team should update "IsProtected = False" 

Step 7
Description: Go to Admin>Client Admin>Client Search and search with the Broker name you created the loan with and Click on Audit Trial and verify the field "IsProtected"
Screen: Client Admin
Test Data: nan
Expected Result:  IsProtected should be False in New column.

Step 8
Description: Click on the Loan tab from the top menu.
Note: Check this on a new loan created with configured broker.
Screen: Loan Summary
Test Data: nan
Expected Result: "Customer Protection Level" should be "No Protection" and it sho

--- RAW DOC 12 ---
TestCaseId: 725217_WHL
Content Preview:

TestCase: 725217_WHL
Channels: WHL, CL1

Pre-Condition & Assumptions:
Created a new loan from Broker Portal with the below pre-conditions : 
1. Channel: CL1
2.Loan Type: VA
3. Product Code: VF30
4. Loan Purpose: Refinance
5. Loan Stage: Created

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT environment
Screen: Login Page
Test Data: https://uath2o.newrez.com/
Expected Result: Login Should be successful

Step 02
Description: Open a Loan which is created as per the preconditions  
Screen: Dashboard
Test Data: nan
Expected Result: Loan summary page should be opened.

Step 03
Description: Navigate to Conditions screen
Screen: Conditions pop-up
Test Data: nan
Expected Result: Conditional panel loads successfully

Step 04
Description: Locate a condition with Broker to Provide checkbox already checked
Screen: Conditions pop-up
Test Data: nan
Expected Result: Broker facing checkbox should checked

Step 05
Description: Click 3-dot menu and select Update Condition
Screen: Conditions pop-up
Test Data: nan
Expected Result: Update Condition screen opens

Step 06
Description: Verify Broker to Provide checkbox is initially checked under "Applicable To" section
Screen: Update Condition
Test Data: nan
Expected Result: Checkbox should checked

Step 07
Description: Manually Uncheck the Broker to Provide checkbox and verify the "Customer Required" and "Internal" checkbox
Screen: Update Condition
Test Data: nan
Expected Result: "Customer Required", "Internal" and "Broker to Provide" Checkboxes should be unchecked and editable


Step 08
Description: Check Customer Required checkbox and verify "Internal"  and "Broker to Provide" checkboxes under "Applicable To" section
Screen: Update Condition
Test Data: nan
Expected Result: • Customer Required should selected
• Internal"  and "Broker to Provide" checkboxes should disabled/grayed out

Step 09
Description: Uncheck Customer Required checkbox and verify the "Broker to Provide" checkbox
Screen: Update Conditi

--- RAW DOC 13 ---
TestCaseId: 659272_659276_CL1_01
Content Preview:

TestCase: 659272_659276_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
1. Channel: CL
2. Loan Purpose: Purchase.
3. Loan Type: Conv.
4. Product:   JRWTSQM30 (YOSMITE)
5. AUS successfully run
6. Loan Stage: "Application Submitted".

User should have privilege 1280.
Loan should be created after Jan release deployment.

=========== TEST STEPS ===========

Step 01
Description: Login to H2O in UAT environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com/
Expected Result: Login should be successful

Step 02
Description: Open the loan as per pre-condition
Screen: nan
Test Data: nan
Expected Result: Loan summary screen should be displayed.

Step 03
Description: Navigate to Screen --> Pre-Underwriting --> Pre-Underwriting Main --> Expiration Dates and verify the "Credit Report Expiration" field.
Screen: Pre-Underwriting Main
Test Data: nan
Expected Result: Credit Report Expiration" date should be auto populate as 'Credit Report Order Date + 120 days' and Credit Report Expiration checkbox should be unchecked

Step 04
Description: Navigate to 1008 --> UW analysis --> Expiration Dates and verify the "Credit Report Order" date and "Credit Report Expiration" field.
Screen: 1008 > UW analysis
Test Data: nan
Expected Result: • "Credit Report Order" date should be displayed.
• Credit Report Expiration" date should be auto populate as 'Credit Report Order Date + 120 days' and Credit Report Expiration checkbox should be unchecked

Step 05
Description: Checks the checkbox next to Credit Report Expiration
Screen: 1008 > UW analysis
Test Data: nan
Expected Result: • Credit Report Expiration checkbox should be checked.
• Credit Report Expiration text field should be clear and disabled.

Step 06
Description: Navigate to Tools > Modernized Audit and verify the 'Credit Report Expiration'
Screen: Modernized Audit
Test Data: nan
Expected Result: • Record of "Credit Report Expiration", previous value should be (step 4) and New value should be displayed as blank.
• Previous value 

--- RAW DOC 14 ---
TestCaseId: 745949_CL1_01
Content Preview:

TestCase: 745949_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
1. CL1 Loan should be created from Blueprint with a valid Broker user id.
2. Product: Any Non-CES/Non-QM

NOTE: Select different broker ID from other scripts- example- Sid.joejones

=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment 
Screen: Login Page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 2
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Go to Admin>Client Admin>Client Search and search with the Broker name you created the loan with.
Screen: Client Admin
Test Data: nan
Expected Result: Broker Admin page should be displayed.

Step 4
Description: Verify below fields under Company Tab:
"IsRezClub"
"Customer Protection Level"
Screen: Broker Admin
Test Data: nan
Expected Result: "IsRezClub" Indicator should be Yes
"Customer Protection Level" Indicator should be "30 Months" and it should be read only.

Step 5
Description: Click on Audit Trial and verify the field "IsProtected"
Screen: Broker Admin
Test Data: nan
Expected Result:  IsProtected should be True in New column.

Step 6
Description: Click on the Loan tab from the top menu.
Screen: Loan Summary
Test Data: nan
Expected Result: "IsRezClub" should be Yes
"Customer Protection Level" should be "30 Months" and it should be read only.

Step 7
Description: Request the BP team to update the "IsRezClub = False" and "IsProtected = True" 
Screen: Broker Admin
Test Data: nan
Expected Result: BP team should update the "IsRezClub = False" and "IsProtected = True" 
"Customer Protection Level" Indicator value should be changed to "18 Months" and it should be read only

Step 8
Description: Go to Admin>Client Admin>Client Search and search with the Broker name you created the loan with.
Screen: Client Admin
Test Data: nan
Expect

--- RAW DOC 15 ---
TestCaseId: 733851_CL1_01
Content Preview:

TestCase: 733851_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create a Loan from Broker Portal:
1. Loan channel: CL1
2. Loan Type - Conventional
3. Loan Purpose - Purchase
4. Loan Product: CF30
5. Loan Stage: "Created".

=========== TEST STEPS ===========

Step 01
Description: Login to H2O-A in UAT environment.
Screen: Login page
Test Data: https://qch2o.caliberdirect.com/
Expected Result: Login should be successful

Step 02
Description: Open the loan created as per the pre-conditions.
Screen: Dashboard
Test Data: nan
Expected Result: Loan summary page should be opened.

Step 03
Description: Navigate to Tools and click on Import
Screen: Loan Summary
Test Data: nan
Expected Result: "Import Over Current Loan" pop-up should be opened

Step 04
Description: Click on "choose file" button and select different borrower xml(MISMO 3.4) that has mismatch in the following filed compared to the current loan
•Primary Borrower Name (First or Last)
•Primary Borrower SSN
•Subject Property Address
Screen: Loan Summary
Test Data: nan
Expected Result: File should be selected

Step 05
Description: Click on "Import" button
Screen: Loan Summary
Test Data: nan
Expected Result: The system should display an error message:
 "Primary Borrower information does not match. Unable to Import."
Note- A red asterisk should be displayed next to each datapoint that does not match

Step 06
Description: Click on cancel button and Under 1003-> Loan Summary-> Subject property address update at the end of address St/Dr/Drive/Street/Ave which is not present on the loan.
Ex: If in XML address is 40 Viking Drive, then update address as 40 Viking Dr
Screen: 1003-> Loan Summary
Test Data: nan
Expected Result: Property address should be updated

Step 07
Description: Click on Tools-> Import and Upload the Same XML
Screen: Tools-> Import
Test Data: nan
Expected Result: XML should be Uploaded.

Step 08
Description: Click on the Import button
Screen: Tools-> Import
Test Data: nan
Expected Result: XML should be

--- RAW DOC 16 ---
TestCaseId: 740128_CL1_01
Content Preview:

TestCase: 740128_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create loan from broker Portal:
1. Channel: CL1
2. Loan Purpose: Any
3. Loan Type: FHA
4. Product Code: Any 
5. Loan Stage: Created
6.Type of Property: Detached
7.Condo Warranty Type: Any

=========== TEST STEPS ===========

Step 01
Description: Log into H2OA in UAT environment.
Screen: nan
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 02
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan 

Step 03
Description: Navigate to status -->  FHA Request Case Number 
Screen: FHA Request Case Number
Test Data: nan
Expected Result:  FHA Request Case Number screen should be opened

Step 04
Description: Navigate to "Type Of Case" section and verify "Is the Property a Condo?" field
Screen: FHA Request Case Number
Test Data: nan
Expected Result: Default option should be selected to "No" for "Is the Property a Condo?" field when property type is selected to "Detached" 

Step 05
Description: Navigate to 1003 > Loan Summary > Property Details. Update the property type to "Detached Condominium" and save it.
Screen: Loan Summary 
Test Data: nan
Expected Result: The property type should be updated and saved successfully. 

Step 06
Description: Navigate to "Type Of Case" section and verify "Is the Property a Condo?" field
Screen: FHA Request Case Number
Test Data: nan
Expected Result: • Default option should be selected to "Yes" for "Is the Property a Condo?" field when property type is selected to "Detached condominium" 
• "Are you requesting a Single Unit Approval (SUA) Condo?" field should be displayed below the "Is the Property a Condo" with default value "No"

Step 07
Description: Select "Are you requesting a Single Unit Approval (SUA) Condo?" = Yes
Screen: FHA Request Case Number
Test Data: nan
Expected Result: • "Is the FHA Concentration > 10%?" fi

--- RAW DOC 17 ---
TestCaseId: 659272_659276_CL_02
Content Preview:

TestCase: 659272_659276_CL_02
Channels: CL1

Pre-Condition & Assumptions:
1. Channel: CL1.
2. Loan Purpose: Purchase.
3. Loan Type: Conv.
4. Product:    JCHL10I (ZION IO)
5. AUS successfully run
6. Loan Stage: "Application Submitted".

User should have privilege 1280.
Loan should be created after Jan release deployment.

=========== TEST STEPS ===========

Step 01
Description: Login to H2O in UAT environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com/
Expected Result: Login should be successful

Step 02
Description: Open the loan as per pre-condition
Screen: nan
Test Data: nan
Expected Result: Loan summary screen should be displayed.

Step 03
Description: Navigate to Screen --> Pre-Underwriting --> Pre-Underwriting Main --> Expiration Dates and verify the "Credit Report Expiration" field.
Screen: Pre-Underwriting Main
Test Data: nan
Expected Result: Credit Report Expiration" date should be auto populate as 'Credit Report Order Date + 120 days' and Credit Report Expiration checkbox should be unchecked

Step 04
Description: Navigate to 1008 --> UW analysis --> Expiration Dates and verify the "Credit Report Order" date and "Credit Report Expiration" field.
Screen: 1008 > UW analysis
Test Data: nan
Expected Result: • "Credit Report Order" date should be displayed.
• Credit Report Expiration" date should be auto populate as 'Credit Report Order Date + 120 days' and Credit Report Expiration checkbox should be unchecked

Step 05
Description: Navigate to Tools > Modernized Audit and verify the 'Credit Report Expiration'
Screen: Modernized Audit
Test Data: nan
Expected Result: • Record of "Credit Report Expiration", previous value should be blank and New value should be displayed as (step 04).
• Previous value should be blank and New value should be "False" for  "Credit Report Expiration Not Required" 

Step 06
Description: Close the Mod Audit and Navigate to Tools >  Audit and verify the ''Credit Report Expiration'& 'Credit Report Order Date' record
Screen: Tra

--- RAW DOC 18 ---
TestCaseId: 719229_CL1_01
Content Preview:

TestCase: 719229_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create a loan from Broker Portal as per pre-conditions
1. Channel: CL1
2. Loan Purpose: Purchase
3. Loan Type: FHA
4. Product Code: Any
5. Loan Stage: "Clear to Close"
Note:
1. PTCD/PTD conditions should have all of this below status "Open", "Clear "," Waive", "Submitted", "Reviewed Deficient",  "Pending UW Review" 


=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment
Screen: Login Page
Test Data: https://uath2o.newrez.com
Expected Result: Login should be successful

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 03
Description: Navigate to Conditions tab and verify PTCD/PTD conditions have all below status:
" Open", "Submitted", "Reviewed Deficient", "Pending UW Review" ,"Clear" and "Waive"
Screen: Conditions Screen
Test Data: nan
Expected Result: PTCD/PTD conditions should have all below status:
 "Open","Submitted", "Reviewed Deficient", "Pending UW Review" ,"Clear" and "Waive"

Step 04
Description: Navigate to Status > CD Collaboration > Fee & Compliance
Screen: CD Collaboration
Test Data: nan
Expected Result: Fee & Compliance screen should be displayed.

Step 05
Description: Scroll down and verify "Order Closing Disclosure" button disabled
Screen: CD Collaboration
Test Data: nan
Expected Result: "Order Closing Disclousre" button should be disabled

Step 06
Description: Verify message "All PTCD conditions are not cleared" when mouse overed on "Order Closing Disclosure" button .
Screen: CD Collaboration
Test Data: nan
Expected Result: "All PTCD conditions are not cleared" when mouse overed on "Order Closing Disclosure"button.

Step 07
Description: Navigate to Conditions tab and update all the open PTCD/PTD conditions to "Clear"and "Waive" .
NOTE:Open conditions includes: "Submitted", "Reviewed Deficient",  "Pending UW Review" ,Ope

--- RAW DOC 19 ---
TestCaseId: 679250_CL1_01
Content Preview:

TestCase: 679250_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create Loan from H2O with below pre-conditions.
1. Channel: CL1
2. Loan Purpose: Purchase
3. Loan Type: VA
4. Product: VF30
5.Brand: Newrez
6. Loan Stage: "Created"


=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT Environment
Screen: nan
Test Data: https://qch2o.caliberdirect.com/Loan/LoanApplication#/LoanSummary
Expected Result: Login should be successful

Step 02
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: Loan number:
Expected Result: Loan Summary screen should be opened for the loan 

Step 03
Description: Verify "Brand" indicator
Screen: Loan Summary
Test Data: nan
Expected Result: Brand name should be displayed in one row

Step 04
Description: Verify "Mark as Priority" indicator
Screen: Loan Summary
Test Data: nan
Expected Result: Indicator should be present with checkbox and should be unchecked

Step 05
Description: Check the "Mark as Priority" indicator checkbox
Screen: Loan Summary
Test Data: nan
Expected Result: Username and Timestamp should not be present when the "Mark as Priority" indicator checkbox is checked.

Step 06
Description: Verify "Brand" indicator
Screen: Loan Summary
Test Data: nan
Expected Result: Brand name should be displayed in one row

Step 07
Description: Navigate to Tools>Audit and verify "Mark as Priority" field
Screen: Audit
Test Data: nan
Expected Result: Field should be present, previous value of the field should be "Blank" and new value should be "True"

Step 08
Description: Navigate to Tools>Modernized Audit and verify "Mark as Priority" field
Screen: Modernized Audit
Test Data: nan
Expected Result: Field should be present, previous value of the field should be "Blank" and new value should be "True"

Step 09
Description: Navigate to 1003>Loan summary screen and uncheck the "Mark as Priority" checkbox
Screen: Loan Summary
Test Data: nan
Expected Result: "Mark as Priority"

--- RAW DOC 20 ---
TestCaseId: 718516_CL1_01
Content Preview:

TestCase: 718516_CL1_01
Channels: CL1

Pre-Condition & Assumptions:
Create a loan from BP:
1. Channel: CL1
2. Loan Purpose: Refinance
3. Loan Type: VA
4. Product Code: Any
5. LTV should be greater than 80%

=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment
Screen: Login Page
Test Data: https://uath2o.newrez.com
Expected Result: Login should be successful

Step 2
Description: Open the created loan which meets the Pre-Condition column 
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened for the loan.

Step 3
Description: Click on "Total Loan Amt" button under "Calculations" section.
Screen: Loan Summary
Test Data: nan
Expected Result: VA PMI Calculation modal should be opened.

Step 4
Description: Select  the value from the below fields:
• MI Payment Type: Borrower Paid
	

Screen: VA PMI Calculation
Test Data: nan
Expected Result: Value should be selected as mentioned.

Step 5
Description: Navigate to 1008 --> Mortgage Insurance and verify the value of the below fields:
• MIP Term
Screen: 1008 --> Mortgage Insurance
Test Data: nan
Expected Result: Some value should be present for the mentioned fields and should be matching with value present in the below locations:
• MIP Term


Step 6
Description: Navigate to Tools --> Modernized Audit and verify the below field values:
• MIP Term

Screen: Modernized Audit
Test Data: nan
Expected Result: Previous values should be displayed as blank and New values should be displayed as per step 5

Step 7
Description: Navigate to 1003 --> Loan Summary screen and increase the LTV where MIP Term will update in 1008 --> Mortgage Insurance screen.
Screen: nan
Test Data: nan
Expected Result: MIP Term should be updated

Step 8
Description: Navigated to Tools --> Modernized Audit and verify the Previous values and new values for below fields
• MIP Term

Screen: nan
Test Data: nan
Expected Result: Previous values should be displayed as per step 05 and New values sh

--- RERANKED ORDER ---
Rank 1: 718523_CL1_01
Rank 2: 647338_647343_CL1_01
Rank 3: 623485_623489_CL1_01
Rank 4: 742390_CL1_01
Rank 5: 738152_CL1_01
Rank 6: 673197_670270_665820_CL1_01
Rank 7: 718524_CL1_01
Rank 8: 737483_CL1_01
Rank 9: 749011_CL1_01
Rank 10: 658373_CL1_01
Rank 11: 745949_CL1_02
Rank 12: 725217_WHL
Rank 13: 659272_659276_CL1_01
Rank 14: 745949_CL1_01
Rank 15: 733851_CL1_01
Rank 16: 740128_CL1_01
Rank 17: 659272_659276_CL_02
Rank 18: 719229_CL1_01
Rank 19: 679250_CL1_01
Rank 20: 718516_CL1_01

--- STRUCTURED EXTRACTION ---
{
  "scenario": "Verify loan details, update dates, and validate changes in Modernized Audit and Closing Details screens.",
  "script": "718523_CL1_01",
  "precondition": "Create a loan from BP with the following details: Channel: CL1, Loan Purpose: Purchase, Loan Type: Conventional, Product Code: Any, Loan stage: UW Submitted. Early Disclosure should be generated, sent via eSign, eSign should be completed, and disclosure should be received.",
  "steps": [
    {
      "stepNo": "1",
      "description": "Log in to H2O-A in UAT Environment.",
      "expectedResult": "Login should be successful."
    },
    {
      "stepNo": "2",
      "description": "Open the created loan which meets the Pre-Condition column.",
      "expectedResult": "Loan Summary screen should be opened for the loan."
    },
    {
      "stepNo": "3",
      "description": "Navigate to DOCS --> Closing Details and check the below dates: Earliest Consummation Date, Most Recent LE Receipt Date, Agent Disbursement Date. Enter some date in Agent Disbursement Date if date is not present.",
      "expectedResult": "Mentioned dates should be present under DOCS --> Closing Details."
    },
    {
      "stepNo": "4",
      "description": "Navigate to Tools --> Modernized Audit and verify the below field values: Earliest Consummation Date, Most Recent LE Receipt Date, Agent Disbursement Date.",
      "expectedResult": "Values should be present in the form of Previous Value and New Value and same as DOCS --> Closing Details."
    },
    {
      "stepNo": "5",
      "description": "Navigate to DIS --> Generate Disclosure and change the 'Receipt Date:' under Transaction History.",
      "expectedResult": "'Receipt Date' should be changed."
    },
    {
      "stepNo": "6",
      "description": "Navigate to DOCS --> Closing Details and verify the 'Most Recent LE Receipt Date' is same as 'Receipt Date'. Again change the Agent Disbursement Date by entering a different date.",
      "expectedResult": "Dates should be updated."
    },
    {
      "stepNo": "7",
      "description": "Navigate to Tools --> Modernized Audit and verify the below field values: Most Recent LE Receipt Date, Agent Disbursement Date.",
      "expectedResult": "Values should be present in the form of Previous Value and New Value for the below dates: Most Recent LE Receipt Date, Agent Disbursement Date."
    },
    {
      "stepNo": "8",
      "description": "Navigate to DOCS --> Generate DOCS and update the 'Earliest Consummation Date'. Note: To update the Earliest Consummation Date, check the Earliest Consummation Date calculation logic Doc.",
      "expectedResult": "'Earliest Consummation Date' should be updated."
    },
    {
      "stepNo": "9",
      "description": "Navigate to Tools --> Modernized Audit and verify the below field values: Earliest Consummation Date.",
      "expectedResult": "Updated values should be displayed correctly in the Modernized Audit in the form of Previous Value and New Value for 'Earliest Consummation Date'."
    },
    {
      "stepNo": "10",
      "description": "Navigate to DOCS --> Closing Details and update the below dates 2-3 times: Earliest Consummation Date, Most Recent LE Receipt Date, Agent Disbursement Date.",
      "expectedResult": "Mentioned dates should be present under DOCS --> Closing Details."
    },
    {
      "stepNo": "11",
      "description": "Navigate to Tools --> Modernized Audit and verify the below field values: Earliest Consummation Date, Most Recent LE Receipt Date, Agent Disbursement Date.",
      "expectedResult": "Values should be present in the form of Previous Value and New Value and same as DOCS --> Closing Details."
    },
    {
      "stepNo": "12",
      "description": "Logout from H2O-A.",
      "expectedResult": "Logout should be successful."
    }
  ]
}

--- FINAL SELECTED STRUCTURED DOC ---
{
  "scenario": "Verify loan details, update dates, and validate changes in Modernized Audit and Closing Details screens.",
  "script": "718523_CL1_01",
  "precondition": "Create a loan from BP with the following details: Channel: CL1, Loan Purpose: Purchase, Loan Type: Conventional, Product Code: Any, Loan stage: UW Submitted. Early Disclosure should be generated, sent via eSign, eSign should be completed, and disclosure should be received.",
  "steps": [
    {
      "stepNo": "1",
      "description": "Log in to H2O-A in UAT Environment.",
      "expectedResult": "Login should be successful."
    },
    {
      "stepNo": "2",
      "description": "Open the created loan which meets the Pre-Condition column.",
      "expectedResult": "Loan Summary screen should be opened for the loan."
    },
    {
      "stepNo": "3",
      "description": "Navigate to DOCS --> Closing Details and check the below dates: Earliest Consummation Date, Most Recent LE Receipt Date, Agent Disbursement Date. Enter some date in Agent Disbursement Date if date is not present.",
      "expectedResult": "Mentioned dates should be present under DOCS --> Closing Details."
    },
    {
      "stepNo": "4",
      "description": "Navigate to Tools --> Modernized Audit and verify the below field values: Earliest Consummation Date, Most Recent LE Receipt Date, Agent Disbursement Date.",
      "expectedResult": "Values should be present in the form of Previous Value and New Value and same as DOCS --> Closing Details."
    },
    {
      "stepNo": "5",
      "description": "Navigate to DIS --> Generate Disclosure and change the 'Receipt Date:' under Transaction History.",
      "expectedResult": "'Receipt Date' should be changed."
    },
    {
      "stepNo": "6",
      "description": "Navigate to DOCS --> Closing Details and verify the 'Most Recent LE Receipt Date' is same as 'Receipt Date'. Again change the Agent Disbursement Date by entering a different date.",
      "expectedResult": "Dates should be updated."
    },
    {
      "stepNo": "7",
      "description": "Navigate to Tools --> Modernized Audit and verify the below field values: Most Recent LE Receipt Date, Agent Disbursement Date.",
      "expectedResult": "Values should be present in the form of Previous Value and New Value for the below dates: Most Recent LE Receipt Date, Agent Disbursement Date."
    },
    {
      "stepNo": "8",
      "description": "Navigate to DOCS --> Generate DOCS and update the 'Earliest Consummation Date'. Note: To update the Earliest Consummation Date, check the Earliest Consummation Date calculation logic Doc.",
      "expectedResult": "'Earliest Consummation Date' should be updated."
    },
    {
      "stepNo": "9",
      "description": "Navigate to Tools --> Modernized Audit and verify the below field values: Earliest Consummation Date.",
      "expectedResult": "Updated values should be displayed correctly in the Modernized Audit in the form of Previous Value and New Value for 'Earliest Consummation Date'."
    },
    {
      "stepNo": "10",
      "description": "Navigate to DOCS --> Closing Details and update the below dates 2-3 times: Earliest Consummation Date, Most Recent LE Receipt Date, Agent Disbursement Date.",
      "expectedResult": "Mentioned dates should be present under DOCS --> Closing Details."
    },
    {
      "stepNo": "11",
      "description": "Navigate to Tools --> Modernized Audit and verify the below field values: Earliest Consummation Date, Most Recent LE Receipt Date, Agent Disbursement Date.",
      "expectedResult": "Values should be present in the form of Previous Value and New Value and same as DOCS --> Closing Details."
    },
    {
      "stepNo": "12",
      "description": "Logout from H2O-A.",
      "expectedResult": "Logout should be successful."
    }
  ]
}

===== RETRIEVAL COMPLETED =====
