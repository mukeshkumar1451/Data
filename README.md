=====================================
ADO INTELLIGENCE ANALYSIS OUTPUT
=====================================

Test Case ID / Test Script ID: 718521_RTL_01  
Test Scenario Id: 718521_SC_01  
Test Scenario Description: Validate the addition of new fields in the Modernized Audit under DIS > Generate Disclosures.  
Test Script Description: This test case validates the presence, functionality, and privilege restrictions of the new fields added to the Modernized Audit under DIS > Generate Disclosures. It ensures that the fields behave as expected based on the acceptance criteria and privilege logic.  
Pre-Condition & Assumptions: Refer to provided precondition context.  

Test Step No. | Test Step Description | Screen Name | Test Data | Expected Results | Requirement Mapping  

Step 01 | Log in to H2O-A in UAT environment | Login | Valid UAT credentials | The system authenticates the user and displays the dashboard | NA  

Step 02 | Open the loan created as per precondition | Loan Summary | Loan Number from precondition | The system loads the loan in editable state | NA  

Step 03 | Navigate to the "Generate Disclosures" section under the "DIS" tab | Loan Navigation Menu | NA | The system displays the "Generate Disclosures" screen with all available fields | 718521_AC_01  

Step 04 | Verify the presence of the "Intent to Proceed" checkbox | Generate Disclosures | NA | The system displays the "Intent to Proceed" checkbox in the "Generate Disclosures" section | 718521_AC_02  

Step 05 | Check the "Intent to Proceed" checkbox | Generate Disclosures | NA | The system allows the checkbox to be selected and retains the selection | 718521_AC_02  

Step 06 | Verify the presence of the "Higher Priced Mortgage Loan" dropdown | Generate Disclosures | NA | The system displays the "Higher Priced Mortgage Loan" dropdown with options: Select, Yes, No | 718521_AC_03  

Step 07 | Select "Yes" from the "Higher Priced Mortgage Loan" dropdown | Generate Disclosures | Yes | The system updates the field value to "Yes" and retains the selection | 718521_AC_03  

Step 08 | Verify the presence of the "HPML DV Override" checkbox | Generate Disclosures | NA | The system displays the "HPML DV Override" checkbox in the "Generate Disclosures" section | 718521_AC_04  

Step 09 | Check the "HPML DV Override" checkbox | Generate Disclosures | NA | The system allows the checkbox to be selected and retains the selection | 718521_AC_04  

Step 10 | Verify the presence of the "Send Via" dropdown | Generate Disclosures | NA | The system displays the "Send Via" dropdown with options: eSign | 718521_AC_05  

Step 11 | Select "eSign" from the "Send Via" dropdown | Generate Disclosures | eSign | The system updates the field value to "eSign" and retains the selection | 718521_AC_05  

Step 12 | Verify the presence of the new fields added to the Modernized Audit | Generate Disclosures | NA | The system displays the new fields in the Modernized Audit section | 718521_AC_06  

Step 13 | Validate the functionality of the new fields in the Modernized Audit | Generate Disclosures | NA | The system allows interaction with the new fields and retains the changes as expected | 718521_AC_07  

Step 14 | Log out from H2O-A | Application Header | NA | The system terminates the session and redirects to the login page | NA
