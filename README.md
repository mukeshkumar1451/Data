### Behavioral Test Case for User Story ID: 718521  
**Title:** Modernized Audit additions - DIS > Generate Disclosures Fields  

---

#### Precondition:  
Create a loan using Mismo 3.4 XML file.  

---

### Test Case Steps  

**Step 01** | **Description:** Log in to H2O-A in UAT1 Environment. | **Screen Name:** Dashboard | **Test Data:** https://qch2o.caliberdirect.com | **Expected Result:** The system should successfully log in to the application. | **Requirement Mapping:** Historical Step 1  

**Step 02** | **Description:** Open the loan created as per the precondition. | **Screen Name:** Loan Summary | **Test Data:** N/A | **Expected Result:** The system should display the Loan Summary screen for the selected loan. | **Requirement Mapping:** Historical Step 2  

**Step 03** | **Description:** Navigate to DIS > Generate Disclosures screen. | **Screen Name:** Generate Disclosure | **Test Data:** N/A | **Expected Result:** The system should display the Generate Disclosure screen. | **Requirement Mapping:** Historical Step 3  

**Step 04** | **Description:** Locate the "Description" field in the H2O UI. | **Screen Name:** Generate Disclosure | **Test Data:** N/A | **Expected Result:** The system should render the "Description" field as a text field without privilege restrictions. | **Requirement Mapping:** AC Transformation  

**Step 05** | **Description:** Locate the "HPML" field in the Generate Disclosure section. | **Screen Name:** Generate Disclosure | **Test Data:** N/A | **Expected Result:** The system should render the "HPML" field as a dropdown containing options "Yes" and "No" without privilege restrictions. | **Requirement Mapping:** AC Transformation  

**Step 06** | **Description:** Locate the "Intent to Proceed" field in the Generate Disclosure section. | **Screen Name:** Generate Disclosure | **Test Data:** N/A | **Expected Result:** The system should render the "Intent to Proceed" field as a checkbox that can be toggled on and off without privilege restrictions. | **Requirement Mapping:** AC Transformation  

**Step 07** | **Description:** Save changes made to the "Intent to Proceed" field. | **Screen Name:** Generate Disclosure | **Test Data:** N/A | **Expected Result:** The system should save the changes successfully. | **Requirement Mapping:** Flow Intelligence  

**Step 08** | **Description:** Perform audit validation for the "Intent to Proceed" field. | **Screen Name:** Generate Disclosure | **Test Data:** N/A | **Expected Result:** The system should log the audit trail for the changes made to the "Intent to Proceed" field. | **Requirement Mapping:** Flow Intelligence  

**Step 09** | **Description:** Confirm that the "Mortgage Broker Fee Agreement" field is not displayed for the RTL channel. | **Screen Name:** Generate Disclosure | **Test Data:** N/A | **Expected Result:** The system should not display the "Mortgage Broker Fee Agreement" field for the RTL channel. | **Requirement Mapping:** Channel Entity Enforcement  

**Step 10** | **Description:** Confirm that the "Mortgage Broker License Type" field is not displayed for the RTL channel. | **Screen Name:** Generate Disclosure | **Test Data:** N/A | **Expected Result:** The system should not display the "Mortgage Broker License Type" field for the RTL channel. | **Requirement Mapping:** Channel Entity Enforcement  

**Step 11** | **Description:** Log out of the application. | **Screen Name:** Dashboard | **Test Data:** N/A | **Expected Result:** The system should log out successfully. | **Requirement Mapping:** Logout Step  

---

### Notes:  
- Steps related to "Mortgage Broker Fee Agreement" and "Mortgage Broker License Type" are excluded due to RTL channel enforcement rules.  
- Behavioral patterns such as save cycles and audit validations are applied where applicable.  
- Dropdown and checkbox interactions are included based on Flow Intelligence.  

---  
**End of Test Case**
