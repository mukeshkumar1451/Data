**Test Case ID / Test Script ID:** 718521_RTL_01  
**Test Scenario Id:** 718521_SC_01  
**Test Scenario Description:** Validate the rendering and behavior of newly added fields in DIS > Generate Disclosures for RTL channel.  
**Test Script Description:** This script validates the rendering, dropdown options, and privilege restrictions of the HPML and Intent to Proceed fields. It ensures proper workflow behavior for RTL channel users.  

**Pre-Condition & Assumptions:**  
1. User has valid credentials and access to the DIS module.  
2. Loan is in a state where disclosures can be generated.  
3. Channel is RTL, and broker-related fields are excluded.  

**Steps:**  

Step 01 | Login | Login Screen | Valid User Credentials | The system grants access to the user dashboard. | Requirement Mapping: 718521_RTL_01  

Step 02 | Open Loan | Loan Dashboard | Loan ID: [Test Loan ID] | The system opens the loan details page. | Requirement Mapping: 718521_RTL_01  

Step 03 | Navigate to Generate Disclosures | Loan Details Page | N/A | The system displays the Generate Disclosures screen. | Requirement Mapping: 718521_RTL_01  

Step 04 | Select Generate Disclosure | Generate Disclosures Screen | N/A | The system displays the Generate Disclosure section. | Requirement Mapping: 718521_RTL_01  

Step 05 | Validate HPML field rendering | Generate Disclosure Section | N/A | The system renders the HPML field as a dropdown. | Requirement Mapping: 718521_RTL_01  

Step 06 | Validate HPML dropdown options | Generate Disclosure Section | N/A | The system displays the dropdown options: Yes, No. | Requirement Mapping: 718521_RTL_01  

Step 07 | Validate HPML privilege restriction | Generate Disclosure Section | N/A | The system ensures the HPML field is not privilege restricted. | Requirement Mapping: 718521_RTL_01  

Step 08 | Navigate to Intent to Proceed | Generate Disclosures Screen | N/A | The system displays the Intent to Proceed section. | Requirement Mapping: 718521_RTL_01  

Step 09 | Validate Intent to Proceed field rendering | Intent to Proceed Section | N/A | The system renders the Intent to Proceed field as a checkbox. | Requirement Mapping: 718521_RTL_01  

Step 10 | Validate Intent to Proceed checkbox behavior | Intent to Proceed Section | N/A | The system allows the checkbox to be checked and unchecked. | Requirement Mapping: 718521_RTL_01  

Step 11 | Validate Intent to Proceed privilege restriction | Intent to Proceed Section | N/A | The system ensures the Intent to Proceed field is not privilege restricted. | Requirement Mapping: 718521_RTL_01  

Step 12 | Logout | User Dashboard | N/A | The system logs the user out and displays the login screen. | Requirement Mapping: 718521_RTL_01
