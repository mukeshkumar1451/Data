```
Test Case ID / Test Script ID: 718523_RTL_01  
Test Scenario Id: 718523_SC_01  
Test Scenario Description: Validate the addition of new fields in the Modernized Audit under Generate Disclosures section, including "Modernized Audit additions."  
Test Script Description: This test case validates the presence, functionality, and behavior of the newly added fields in the Modernized Audit under the Generate Disclosures section. It ensures that the fields are displayed correctly, are functional, and adhere to the business rules defined in the acceptance criteria.  
Pre-Condition & Assumptions: Refer to provided precondition context.  

Test Step No. | Test Step Description | Screen Name | Test Data | Expected Results | Requirement Mapping  

Step 01 | Log in to H2O-A in UAT environment | Login | Valid UAT credentials | The system authenticates the user and displays the dashboard | NA  

Step 02 | Open the loan created as per precondition | Loan Summary | Loan Number from precondition | The system loads the loan in editable state | NA  

Step 03 | Navigate to the "Generate Disclosures" section under the "DIS" menu | Loan Navigation Menu | NA | The system displays the Generate Disclosures page with all available fields and options | 718523_AC_01  

Step 04 | Verify the presence of the "Intent to Proceed" checkbox | Generate Disclosures | NA | The system displays the "Intent to Proceed" checkbox in the Generate Disclosures section | 718523_AC_02  

Step 05 | Select the "Intent to Proceed" checkbox | Generate Disclosures | NA | The system allows the checkbox to be selected and retains the selection | 718523_AC_02  

Step 06 | Verify the presence of the "Higher Priced Mortgage Loan" dropdown | Generate Disclosures | NA | The system displays the "Higher Priced Mortgage Loan" dropdown with options: Select..., Yes, No | 718523_AC_03  

Step 07 | Select "Yes" from the "Higher Priced Mortgage Loan" dropdown | Generate Disclosures | Yes | The system allows the selection and retains the value | 718523_AC_03  

Step 08 | Verify the presence of the "HPML DV Override" checkbox | Generate Disclosures | NA | The system displays the "HPML DV Override" checkbox in the Generate Disclosures section | 718523_AC_04  

Step 09 | Select the "HPML DV Override" checkbox | Generate Disclosures | NA | The system allows the checkbox to be selected and retains the selection | 718523_AC_04  

Step 10 | Verify the presence of the "Send Via" dropdown | Generate Disclosures | NA | The system displays the "Send Via" dropdown with options: eSign | 718523_AC_05  

Step 11 | Select "eSign" from the "Send Via" dropdown | Generate Disclosures | eSign | The system allows the selection and retains the value | 718523_AC_05  

Step 12 | Verify the presence of the "Mortgage Broker Fee/Compensation Agreement" section | Mortgage Broker Fee/Compensation Agreement | NA | The system displays the "Mortgage Broker Fee/Compensation Agreement" section with all relevant fields | 718523_AC_06  

Step 13 | Verify the presence of the "Do you want to include Mortgage Broker Fee/Compensation Agreement in the Newrez LE Package?" field | Mortgage Broker Fee/Compensation Agreement | NA | The system displays the field with options: Select..., Yes, No | 718523_AC_06  

Step 14 | Select "Yes" for the "Do you want to include Mortgage Broker Fee/Compensation Agreement in the Newrez LE Package?" field | Mortgage Broker Fee/Compensation Agreement | Yes | The system allows the selection and retains the value | 718523_AC_06  

Step 15 | Verify the presence of the "Mortgage Broker License Type" field | Mortgage Broker Fee/Compensation Agreement | NA | The system displays the "Mortgage Broker License Type" field with options: Select..., CFL, DRE, RML | 718523_AC_07  

Step 16 | Select "CFL" for the "Mortgage Broker License Type" field | Mortgage Broker Fee/Compensation Agreement | CFL | The system allows the selection and retains the value | 718523_AC_07  

Step 17 | Verify that the "Mortgage Broker License Type" field appears only when SubPropState = CA | Mortgage Broker Fee/Compensation Agreement | SubPropState = CA | The system displays the "Mortgage Broker License Type" field only when SubPropState = CA | 718523_AC_08  

Step 18 | Verify that the "Mortgage Broker License Type" field is privilege restricted | Mortgage Broker Fee/Compensation Agreement | NA | The system restricts access to the field based on user privileges | 718523_AC_09  

Step 19 | Verify the presence of the "Generate Disclosures Fields" section | Generate Disclosures | NA | The system displays all fields under the "Generate Disclosures Fields" section, including "Intent to Proceed," "Higher Priced Mortgage Loan," and "HPML DV Override" | 718523_AC_10  

Step 20 | Verify that all fields under "Generate Disclosures Fields" adhere to the business rules | Generate Disclosures | NA | The system ensures that all fields under "Generate Disclosures Fields" are functional and adhere to the defined business rules | 718523_AC_11  

Step 21 | Verify the addition of "Modernized Audit additions" in the Generate Disclosures section | Generate Disclosures | NA | The system displays the "Modernized Audit additions" fields as per the acceptance criteria | 718523_AC_12  

Step 22 | Log out from H2O-A | Application Header | NA | The system terminates the session and redirects to the login page | NA  
```
