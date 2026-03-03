===== RETRIEVAL DEBUG LOG =====

----- QUERY SENT TO VECTOR DB -----

User Story: Update Company/Originator dropdowns to be paginated pickers in 'Create Loan on Behalf of' loan creation screen
Description: As a
 User

I want 
to update the 'Select a Company' and 'Originator' dropdowns that exist in the 'Create Loan on Behalf of' screen to be paginated Pickers  
 
 
So that
 this screen performs better by not needing to load as much data in a single dropdown 
 
Present Day, users accessing this screen may (based on their individual level of access) come across lengthy loading times due to the number of Companies that can appear in the 'Select a Company' dropdown.  
 
Using monitoring, you can see that it takes over 20 seconds of loading the 'companiesforuser' when trying to load the screen for myself (I have access to all WHL Business Units) 
 
It actually took just over 30 seconds to complete -  
 
 
Most WHL users who would create loans in this screen also have access to many different WHL Business Units and would see lengthy loading times as well. Updating this screen to use paginated pickers would cut down on this loading time significantly. 
 
General UI Mockup - 
 
 
*'Select a Company' picker should be able to be clicked in the above state. 'Originator' picker should be greyed out until a Company has been selected via the 'Select a Company' picker.
 
'Select a Company' picker mockup - 
 
 
--This picker should have columns for 'Broker Id' and 'Company' 
--This picker should contain all values that the dropdown normally would given the logged in user's access levels. 
 
--Picker should display 5 items at a time by default. Users can choose to display either 5, 10, or 20 items in a single page, and screen should display total list of items given the selected number of pages. (same as above mockup) 
--Users should be able to enter search criteria with results to appear after clicking 'Search'. Search function should return partial matches (For example - if user searches for 35, system should return BID 35 as well as BID 355 (if they exist) as well as Company "35th St Loans" (if they exist)) 
--After user clicks on a result from the list, popup should close and selected Company should pull into the 'Select a Company' field 
--Clicking 'Close' without selecting a result from the list should close out the popup without updating the 'Select a Company' field 
 
After selecting a Company, users should then be able to click on the 'Originator' picker.
 
'Originator' picker mockup - 
 
*Originator in this use case = Broker LO
 
--This picker should have columns for 'Id', 'First Name', 'Last Name', and 'Office Phone' 
--This picker sh
ould contain all active Broker Employees for the selected Company
 
--Picker should display 5 items at a time by default. Users can choose to display either 5, 10, or 20 items in a single page, and screen should display total list of items given the selected number of pages. (same as above mockup) 
--Users should be able to enter search criteria with results to appear after clicking 'Search'. Search function should return partial matches similar to what was mentioned in 'Company' picker section. 
*Valid Search Criteria should include ID, First Name, Last Name, or Office Phone
 
--After user clicks on a result from the list, popup should close and selected Originator should pull into the 'Originator' field 
--Clicking 'Close' without selecting a result from the list should close out the popup without updating the 'Originator' field 

Notes:
 
-Screen should look similar to how 'Rep & Branch' interface behaves now - when a picker is opened, the selected picker should popup in the center of the screen and the background should slightly grey itself out.  
Example of current state 'Rep & Branch' screen with picker open -  
 
-Broker users who also see this interface do not have access to 'Select a Company' so they would be considered out of scope. We 
should not
 
update
 
their UI. 
 
 
-If a logged in user only has a single Company that would be available for picking, 'Select a Company' picker should intelligently default to that single Company. 
-Same behavior should be coded in for 'Originator' picker. If a selected Company has only one active Originator selection available, the 'Originator' picker should default to that selection.
Acceptance Criteria: Select a Company
Navigate to Create Loan on Behalf of Screen.
Verify that the "Select a Company" field is rendered as a Picker.
Verify that the field is restricted based on user privilege.

Originator
Navigate to Create Loan on Behalf of Screen.
Verify that the "Originator" field is rendered as a Picker.
Verify that the field is restricted based on user privilege.



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
TestCaseId: 742390_WHL_01
Content Preview:

TestCase: 742390_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a new loan from Broker Portal with below pre-conditions:
1. Channel: WHL
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

--- RAW DOC 3 ---
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

--- RAW DOC 4 ---
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

--- RAW DOC 5 ---
TestCaseId: 742390_WHL_02
Content Preview:

TestCase: 742390_WHL_02
Channels: WHL

Pre-Condition & Assumptions:
Create a new  loan in H2O-A as per  below pre-conditions:
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product: VF30
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
Expected Result: Audit entry should be created capturing below:
- Us

--- RAW DOC 6 ---
TestCaseId: 745949_WHL_01
Content Preview:

TestCase: 745949_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
1. WHL Loan should be created from Blueprint with a valid Broker user id.
2. Product: Any Non-CES/Non-QM (VF30)

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

--- RAW DOC 7 ---
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

--- RAW DOC 8 ---
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

--- RAW DOC 9 ---
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

--- RAW DOC 10 ---
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

--- RAW DOC 11 ---
TestCaseId: 745949_WHL_03
Content Preview:

TestCase: 745949_WHL_03
Channels: WHL

Pre-Condition & Assumptions:
1. WHL Loan should be created from Blueprint with a valid Broker user id.
2. Product: FF30 (Any IsNonQM<> True)

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
Expected Result: "Customer Protection Level" should be 30 Months or 18 Months based on RezClub Flag being Yes or No and it should be read only
Note: Check this when RezClub is set to True and set to False

Step 6
Description: Change the product to any Non-QM Product such that IsNonQm becomes True under Audit.
Screen: Loan Summary
Test Data: nan
Expected Result: Non-QM product should be assigned.

Step 7
Description: Click on the Loan tab from the top menu.
Note: Check this on a new loan created with configured broker.
Note: Raise Requirement to Dev Team if the Customer Protection Level is not updated dynamically.
Screen: Loan Summary
Test Data: nan
Expected Result: "Customer Protection Level" should be "No Protection" and it should be read only
Note: Check this when RezClub is set to True and set to False

Step 8
Description: Change the product back to CF30 such that Is

--- RAW DOC 12 ---
TestCaseId: 738432_WHL_02
Content Preview:

TestCase: 738432_WHL_02
Channels: WHL

Pre-Condition & Assumptions:
Admin > Organization Admin > Business Unit user should mark "Is Test Business Unit" = true for Business Unit given below:

URLA Test Division (WHL-testURLA)

Loan should be created through Broker Portal with below pre-cond: use Test.Whlcl1.urla user to create loan
1. Channel: Whl
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: CF30
5. Loan Stage: "Created"

=========== TEST STEPS ===========

Step 01
Description: Log in to H2O-A in UAT1 Environment
Screen: Login Page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 02
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: "Loan Summary" screen should be opened

Step 03
Description: Navigate to 1003 > Loan Summary > Company Details and verify the  "Test Loan Type" field
Screen: 1003
Test Data: nan
Expected Result: Loan's "Test Loan Type" field should be automatically set to "Temporary Test Loan"

Step 04
Description: Navigate to Tool > Audit
Screen: Audit
Test Data: nan
Expected Result: Audit popup should be displayed.

Step 05
Description: Verify the "Test Loan Type" field value is captured
Screen: Audit
Test Data: nan
Expected Result: Traditional audit log should reflect the corresponding value for that field

Step 06
Description: Logout from H2OA.
Screen: H2O A
Test Data: nan
Expected Result: Logout should be successful.


--- RAW DOC 13 ---
TestCaseId: 733851_WHL_01
Content Preview:

TestCase: 733851_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a Loan from Broker Portal:
1. Loan channel: WHL
2. Loan Type - FHA
3. Loan Purpose - Purchase
4. Loan Product: FF30
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
Description: Click on Cancel Button and Under 1003-> Loan Summary-> Subject property address update at the end of address St/Dr/Drive/Street/Ave which is not present on the loan.
Ex: If in XML address is 157 Brownell Road, then update address as 157 Brownell Rd
Screen: 1003-> Loan Summary
Test Data: nan
Expected Result: Property address should be updated

Step 07
Description: Click on Tools-> Import and Upload the Same XML as the loan created
Screen: Tools-> Import
Test Data: nan
Expected Result: XML should be Uploaded.

Step 08
Description: Click on the Import button
Screen: Tools-> Import
Test Data: nan
Expected Resul

--- RAW DOC 14 ---
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

--- RAW DOC 15 ---
TestCaseId: 619737_WHL_01
Content Preview:

TestCase: 619737_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a new loan using below pre-conditions
1. Channel: WHL
2. Loan Purpose: Refinance
3. Loan Type: Conventional
4. Loan Stage: Created
5. No. of Borrower: Any
6. Product: CF30
7. Browser: Edge

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
Description: Navigate to Status>AUS> Run LPA.

Note: Note down the test result (Accept/Eligible or Accept/Ineligible, etc.). User can manipulate loan data to produce various results)
Screen: Clear Choice AUS
Test Data: nan
Expected Result: LPA should be successful with result.

Step 04
Description: Navigate to Tool>Modernized Audit screen.
Screen: Modernized Audit
Test Data: nan
Expected Result: Modernized Audit screen should be opened.

Step 05
Description: Select 'AUS Recommendation' and 'Underwriting Method' for Field and click on Search.
Screen: Modernized Audit
Test Data: nan
Expected Result: For AUS Recommendation Previous value should be blank and New Value should be Step 3 AUS Result and Underwriting Method previous value should be blank and New Value should be LPA .

Step 06
Description: Close the Modernized Audit screen, click on Tools>Audit screen.
Note:1. Values should be numerical values as per AUS Recommendation dropdown and AUS type selection radio button values.
Screen: Audit
Test Data: nan
Expected Result: AURecommendationID' and 'Underwriting Method' shows the previous and new values.
Note: ID values for AUS Recommendation to be referred  from the file received from onshore and ensure values are matching.
Please note that the value for GUS is 8, LP is 3, Other is 4, DU is 2 and non

--- RAW DOC 16 ---
TestCaseId: 663168_663170_WHL_01
Content Preview:

TestCase: 663168_663170_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a loan from Blueprint in H2OD workflow:
1. Channel: WHL 
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product Code: Any
5. Loan Stage: "UW Submitted"
6. Ops Center: DTB Ops Dallas
7. H2OD processing and Underwriting flag - Y
8. IDUW activity should be triggered and assigned to the user
9. Validation event "Approve with Condition H2O D" is configured in Stone water admin tool as an Error for DV "Missing CPL Date"


=========== TEST STEPS ===========

Step 1
Description: Login to Activity Dashboard with H2OD UW user assigned to the IDUW activity
Screen: Activity Dashboard
Test Data: https://uat-h2od.caliberhomeloans.com/
Expected Result: Login should be successful

Step 2
Description: Click on the loan number under "Initial Decision Activities Assigned to Me" section and verify "Initial Decision" activity is opened
Screen: Activity Dashboard
Test Data: nan
Expected Result: "Initial Decision" activity should be opened 

Step 3
Description: Expand the Collateral>click on Verify the Appraisal and Property>Click on Appraisal and Property.
Screen: H2OD>Middle panel Appraisal and Property
Test Data: nan
Expected Result: Appraisal and Property screen should be opened in middle panel.

Step 4
Description: Navigate to Screens>Pre-Underwriting>Pre-Underwriting Main>Under Expiration Dates section> 'CPL Date' is present.
Note:'Assign the IDUW activity with another user without priv 1280  and validate CPL Date is enable with the user'
Screen: H2OD>Middle panel Pre-Underwriting Main
Test Data: nan
Expected Result: CPL Date' should be blank.

Step 5
Description: Complete all the tasks except Activity Disposition
Screen: Activity Dashboard
Test Data: nan
Expected Result: All tasks should be completed except Activity Disposition

Step 6
Description: Under Activity Disposition > select disposition as "Approved with Conditions" and click on "Confirm Disposition"
Screen: Loan Overview
Test Data:

--- RAW DOC 17 ---
TestCaseId: 740712_Regression_WHL_01
Content Preview:

TestCase: 740712_Regression_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a loan from Broker Portal with the below Pre-conditions:
1. Channel: WHL
2. Loan Type: Conventional
3. Product Code: NRSEF30
4. Loan Purpose: Purchase
5. Loan stage : Created
6. No.of Borrowers : Any
7.  Investor Access Loan: Yes
Note: Verify Investor Access is checked & DSCR Applicable not checked in product admin tool for the assigned product.

=========== TEST STEPS ===========

Step 01
Description: Login to H2O-A Application
Screen: Login Page
Test Data: https://uath2o.newrez.com/
Expected Result: Login should be successful

Step 02
Description: Open a loan which is created as per Preconditions
Screen: Dashboard
Test Data: nan
Expected Result: Loan should be opened and loan summary page should be opened

Step 03
Description: Navigate to the 1003 screen
Screen: 1003
Test Data: nan
Expected Result: 1003 screen should displayed

Step 04
Description: Verify Emp, Inc. & Housing is present.
Screen: 1003
Test Data: nan
Expected Result: Emp, Inc. & Housing tab should be displayed.

Step 05
Description: Confirm Rental Income tab is not shown
Screen: 1003
Test Data: nan
Expected Result: Rental Income tab should not be present.

Step 07
Description: Logout of the application
Screen: Logout
Test Data: nan
Expected Result: Logout should be successful


--- RAW DOC 18 ---
TestCaseId: 679250_WHL_01
Content Preview:

TestCase: 679250_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create Loan from Broker portal with below pre-conditions.
1. Channel: WHL
2. Loan Purpose: Purchase
3. Loan Type: Conventional
4. Product: Any
5.Brand: Newrez
6. Loan Stage: "UW submitted"
7. H2O-D Processing and Underwriting Flag should be ‘Y’ 
8. IDUW activity should present on Manger DB and assigned to user.

=========== TEST STEPS ===========

Step 01
Description: Login to H2O-D as above Underwriter user.
Screen: nan
Test Data: https://uat-h2od.caliberhomeloans.com
Expected Result: Login should be successful

Step 02
Description: Navigate to 'Initial Decision Activities Assigned to Me 'section and click on loan number.
Screen: nan
Test Data: Loan number:
Expected Result: IDUW activity should be open for the loan

Step 03
Description: Go to Loan Analysis task step and click on "complete UW analysis"
Screen: nan
Test Data: nan
Expected Result: Loan should be open in middle panel 

Step 04
Description: Verify "Brand" indicator
Screen: Loan Summary
Test Data: nan
Expected Result: Brand name should be displayed in one row

Step 05
Description: Verify "Mark as Priority" indicator
Screen: Loan Summary
Test Data: nan
Expected Result: Indicator should be present with checkbox and should be unchecked

Step 06
Description: Check the "Mark as Priority" indicator checkbox
Screen: Loan Summary
Test Data: nan
Expected Result: Username and Timestamp should not be present when the "Mark as Priority" indicator checkbox is checked.

Step 07
Description: Verify "Brand" indicator
Screen: Loan Summary
Test Data: nan
Expected Result: Brand name should be displayed in one row

Step 08
Description: Navigate to Tools>Audit and verify "Mark as Priority" field
Screen: Audit
Test Data: nan
Expected Result: Field should be present, previous value of the field should be "Blank" and new value should be "True"

Step 09
Description: Navigate to Tools>Modernized Audit and verify "Mark as Priority" field
Screen: Modernized Audit


--- RAW DOC 19 ---
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


--- RAW DOC 20 ---
TestCaseId: 606241_WHL_01
Content Preview:

TestCase: 606241_WHL_01
Channels: WHL

Pre-Condition & Assumptions:
Create a loan using Mismo 3.4 XML file.
Pre-Conditions:
1. Channel: WHL
2. Loan Purpose: Any
3. Loan Type: Any
5. Document type: Any
6. Loan Stage: "Created" 
7. Product: Any
8: Browser: Chrome.

=========== TEST STEPS ===========

Step 1
Description: Log in to H2O-A in UAT Environment
Screen: Dashboard
Test Data: https://qch2o.caliberdirect.com
Expected Result: Login should be successful 

Step 2
Description: Ensure Dashboard logo is changed.
Screen: Dashboard
Test Data: nan
Expected Result: Dashboard logo should be changed from Caliber to Newrez.

Step 3
Description: Ensure H2O logo name is changed.
Screen: Dashboard
Test Data: nan
Expected Result: In H2O logo name should be changed from Caliber to Newrez.

Step 4
Description: Ensure footer and copyrights is changed.
Screen: Dashboard
Test Data: nan
Expected Result: Footer and copyrights should be changed from Caliber to Newrez.

Step 5
Description: Click on 'Newrez Website User Agreement'.
Screen: Agreement
Test Data: nan
Expected Result: In Agreement page below should be replaced.
->Replace “Caliber Home Loans, Inc.” with “Newrez LLC”.
->All ‘Caliber’ mentions with ‘Newrez.’

Step 6
Description: Ensure Browser title is changed to Newrez.
Screen: Dashboard
Test Data: nan
Expected Result: Browser title should be changed from Caliber Home Loans to Newrez.

Step 7
Description: Logout from H2O and relogin with different User and validate same steps from 2 to 6.
Screen: Dashboard
Test Data: nan
Expected Result: Relogin should be successful and validations should be done from step 2 to 6.

Step 8
Description: Open the loan which is created as per the Pre-Conditions.
Screen: Loan Summary
Test Data: nan
Expected Result: Loan Summary screen should be opened

Step 9
Description: Hover Over on 'H2ONLINE Powered By Newrez'.
Screen: Loan Summary
Test Data: nan
Expected Result: Hover Over message should be 'Newrez'.

Step 10
Description: Navigate to Status>AU

--- RERANKED ORDER ---
Rank 1: 647338_647343_WHL_01
Rank 2: 564936_564981_WHL_01
Rank 3: 565797_WHL_01
Rank 4: 742390_WHL_01
Rank 5: 742390_WHL_02
Rank 6: 745949_WHL_01
Rank 7: 738152_WHL_02
Rank 8: 623485_623489_WHL_01
Rank 9: 738152_WHL_01
Rank 10: 573135_WHL_02
Rank 11: 745949_WHL_03
Rank 12: 738432_WHL_02
Rank 13: 733851_WHL_01
Rank 14: 725217_WHL
Rank 15: 619737_WHL_01
Rank 16: 663168_663170_WHL_01
Rank 17: 740712_Regression_WHL_01
Rank 18: 679250_WHL_01
Rank 19: 623485_623489_WHL_Pipeline_01
Rank 20: 606241_WHL_01

--- STRUCTURED EXTRACTION ---
{
  "scenario": "Verify the functionality of Government Insuring > Insurance Tracking screen and Modernized Audit in H2O-D application.",
  "script": "647338_647343_WHL_01",
  "precondition": "1. The loan should be part of the new model under the H2O digital workflow and created from Broker portal.\n2. Loan channel: WHL.\n3. Ops Center: DTB Ops Dallas.\n4. Loan Type - Conventional.\n5. Loan Purpose - Refinance.\n6. Loan Product - CF30.\n7. H2O-D Processing and UW Flag should be \u2018Y\u2019.\n8. Loan Stage - 'UW Submitted'.\n9. Loan should have IDUW activity assigned to a User which has all IDUW skill and does not have privilege 1016.",
  "steps": [
    {
      "stepNo": "01",
      "description": "Login to H2O-D user dashboard as assigned IDUW activity and click on Loan number under 'Initial Decision' queue.",
      "expectedResult": "CRUW activity should be opened."
    },
    {
      "stepNo": "02",
      "description": "Expand 'Credit' section and click on verify loan details.",
      "expectedResult": "1003-> Loan Summary screen should be opened in the middle panel."
    },
    {
      "stepNo": "03",
      "description": "Navigate to Screens-> Government Insuring > Insurance Tracking screen and verify the fields.",
      "expectedResult": "Below fields should be present:\n\u2022 \u2018Government insuring received date\u2019 label is renamed to \u2018Government Audit Received Date\u2019.\n\u2022 A new 'date picker' field with label \u2018Post-Closing Reviewed Date\u2019 should be present under existing \u2018Agency Case Number\u2019 field.\n\u2022 \u2018Government insuring review date\u2019 label is renamed to \u2018Government Insuring Review Date\u2019."
    },
    {
      "stepNo": "04",
      "description": "Verify the below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date",
      "expectedResult": "All the fields should be disabled."
    },
    {
      "stepNo": "05",
      "description": "Assign privilege 1016 to the IDUW user.",
      "expectedResult": "Privilege should be assigned."
    },
    {
      "stepNo": "06",
      "description": "Navigate to Screens-> Government Insuring > Insurance Tracking screen and verify the below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date",
      "expectedResult": "All the fields should be enabled."
    },
    {
      "stepNo": "07",
      "description": "Click on the 'Post-Closing Reviewed Date' field and select a date and click on Save.",
      "expectedResult": "\u2022 Date Picker should be opened.\n\u2022 Selected date should be saved and system should display the User name, date and timestamp next to the field."
    },
    {
      "stepNo": "08",
      "description": "Click on the \u2018Government Audit Received Date' field and select a date and click on Save.",
      "expectedResult": "\u2022 Date Picker should be opened.\n\u2022 Selected date should be saved and System should display the User name, date and timestamp next to the field."
    },
    {
      "stepNo": "09",
      "description": "Click on the \u2018Government Insuring Review Date\u2019 field and select a date and click on Save.",
      "expectedResult": "\u2022 Date Picker should be opened.\n\u2022 Selected date should be saved and System should display the User name, date and timestamp next to the field."
    },
    {
      "stepNo": "10",
      "description": "Navigate to Tools-> Modernized Audit and verify the grid.",
      "expectedResult": "Data grid, previous value should be displayed as blank and New value should be selected dates for below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date"
    },
    {
      "stepNo": "11",
      "description": "Update the different values for the below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date",
      "expectedResult": "Field values should be updated."
    },
    {
      "stepNo": "12",
      "description": "Navigate to Tools-> Modernized Audit, select option \u2018Loan\u2019 in the \u2018View\u2019 dropdown field and verify the grid.",
      "expectedResult": "All fields should be displayed in the grid with 'Previous value' and 'New Value' including below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date"
    },
    {
      "stepNo": "13",
      "description": "Navigate to Tools-> Modernized Audit, select option \u2018Loan\u2019 in the \u2018View\u2019 dropdown field and verify the 'Field' dropdown field.",
      "expectedResult": "Below option should be listed in 'Field' dropdown field:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date"
    },
    {
      "stepNo": "14",
      "description": "Select the below options from 'Field' dropdown and click on 'Search' button:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date",
      "expectedResult": "Data grid should be displayed with previous value as previously selected dates and New value should be displayed value only for below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date"
    },
    {
      "stepNo": "15",
      "description": "Navigate to Tools-> Audit screen and Search the Below fields and verify the values:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date",
      "expectedResult": "Data grid should be displayed with previous value as previously selected dates and New value should be updated value only for below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date"
    },
    {
      "stepNo": "16",
      "description": "Logout H2OA Application.",
      "expectedResult": "Logout should be successful."
    }
  ]
}

--- FINAL SELECTED STRUCTURED DOC ---
{
  "scenario": "Verify the functionality of Government Insuring > Insurance Tracking screen and Modernized Audit in H2O-D application.",
  "script": "647338_647343_WHL_01",
  "precondition": "1. The loan should be part of the new model under the H2O digital workflow and created from Broker portal.\n2. Loan channel: WHL.\n3. Ops Center: DTB Ops Dallas.\n4. Loan Type - Conventional.\n5. Loan Purpose - Refinance.\n6. Loan Product - CF30.\n7. H2O-D Processing and UW Flag should be \u2018Y\u2019.\n8. Loan Stage - 'UW Submitted'.\n9. Loan should have IDUW activity assigned to a User which has all IDUW skill and does not have privilege 1016.",
  "steps": [
    {
      "stepNo": "01",
      "description": "Login to H2O-D user dashboard as assigned IDUW activity and click on Loan number under 'Initial Decision' queue.",
      "expectedResult": "CRUW activity should be opened."
    },
    {
      "stepNo": "02",
      "description": "Expand 'Credit' section and click on verify loan details.",
      "expectedResult": "1003-> Loan Summary screen should be opened in the middle panel."
    },
    {
      "stepNo": "03",
      "description": "Navigate to Screens-> Government Insuring > Insurance Tracking screen and verify the fields.",
      "expectedResult": "Below fields should be present:\n\u2022 \u2018Government insuring received date\u2019 label is renamed to \u2018Government Audit Received Date\u2019.\n\u2022 A new 'date picker' field with label \u2018Post-Closing Reviewed Date\u2019 should be present under existing \u2018Agency Case Number\u2019 field.\n\u2022 \u2018Government insuring review date\u2019 label is renamed to \u2018Government Insuring Review Date\u2019."
    },
    {
      "stepNo": "04",
      "description": "Verify the below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date",
      "expectedResult": "All the fields should be disabled."
    },
    {
      "stepNo": "05",
      "description": "Assign privilege 1016 to the IDUW user.",
      "expectedResult": "Privilege should be assigned."
    },
    {
      "stepNo": "06",
      "description": "Navigate to Screens-> Government Insuring > Insurance Tracking screen and verify the below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date",
      "expectedResult": "All the fields should be enabled."
    },
    {
      "stepNo": "07",
      "description": "Click on the 'Post-Closing Reviewed Date' field and select a date and click on Save.",
      "expectedResult": "\u2022 Date Picker should be opened.\n\u2022 Selected date should be saved and system should display the User name, date and timestamp next to the field."
    },
    {
      "stepNo": "08",
      "description": "Click on the \u2018Government Audit Received Date' field and select a date and click on Save.",
      "expectedResult": "\u2022 Date Picker should be opened.\n\u2022 Selected date should be saved and System should display the User name, date and timestamp next to the field."
    },
    {
      "stepNo": "09",
      "description": "Click on the \u2018Government Insuring Review Date\u2019 field and select a date and click on Save.",
      "expectedResult": "\u2022 Date Picker should be opened.\n\u2022 Selected date should be saved and System should display the User name, date and timestamp next to the field."
    },
    {
      "stepNo": "10",
      "description": "Navigate to Tools-> Modernized Audit and verify the grid.",
      "expectedResult": "Data grid, previous value should be displayed as blank and New value should be selected dates for below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date"
    },
    {
      "stepNo": "11",
      "description": "Update the different values for the below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date",
      "expectedResult": "Field values should be updated."
    },
    {
      "stepNo": "12",
      "description": "Navigate to Tools-> Modernized Audit, select option \u2018Loan\u2019 in the \u2018View\u2019 dropdown field and verify the grid.",
      "expectedResult": "All fields should be displayed in the grid with 'Previous value' and 'New Value' including below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date"
    },
    {
      "stepNo": "13",
      "description": "Navigate to Tools-> Modernized Audit, select option \u2018Loan\u2019 in the \u2018View\u2019 dropdown field and verify the 'Field' dropdown field.",
      "expectedResult": "Below option should be listed in 'Field' dropdown field:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date"
    },
    {
      "stepNo": "14",
      "description": "Select the below options from 'Field' dropdown and click on 'Search' button:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date",
      "expectedResult": "Data grid should be displayed with previous value as previously selected dates and New value should be displayed value only for below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date"
    },
    {
      "stepNo": "15",
      "description": "Navigate to Tools-> Audit screen and Search the Below fields and verify the values:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date",
      "expectedResult": "Data grid should be displayed with previous value as previously selected dates and New value should be updated value only for below fields:\n\u2022 Government Audit Received Date\n\u2022 Post-Closing Reviewed Date\n\u2022 Government Insuring Review Date"
    },
    {
      "stepNo": "16",
      "description": "Logout H2OA Application.",
      "expectedResult": "Logout should be successful."
    }
  ]
}

===== RETRIEVAL COMPLETED =====
