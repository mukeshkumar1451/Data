### Test Case ID / Test Script ID: 718521_RTL_01  
### Test Scenario Id: 718521_SC_01  
### Test Scenario Description: Validate the addition of new fields in the Modernized Audit under DIS > Generate Disclosures.  
### Test Script Description: This test script validates the visibility, functionality, and business rules of the newly added fields in the Modernized Audit section under DIS > Generate Disclosures. It includes positive and negative scenarios for field interactions and privilege restrictions.  
### Pre-Condition & Assumptions:  
Refer to provided precondition context:  
1. Channel: Retail  
2. Loan Type: Conventional  
3. Product Code: Any  
4. Branch Name: Ret-testURLA-URLA  
5. Loan stage: Application Accepted.  
6. Loan should not be locked.  
7. ComplianceEase toggle switch is turned On.  
8. Brand: "Platinum Eagle Mortgage LLC"  

---

### Test Steps  

#### Step 01  
**Test Step Description:** Log in to H2O-A in UAT environment.  
**Screen Name:** Login  
**Test Data:** Valid UAT credentials  
**Expected Results:** The system authenticates the user and displays the dashboard.  
**Requirement Mapping:** NA  

---

#### Step 02  
**Test Step Description:** Open the loan created as per precondition.  
**Screen Name:** Loan Summary  
**Test Data:** Loan Number from precondition  
**Expected Results:** The system loads the loan in editable state.  
**Requirement Mapping:** NA  

---

#### Step 03  
**Test Step Description:** Navigate to DIS > Generate Disclosures.  
**Screen Name:** Left Navigation Panel  
**Test Data:** NA  
**Expected Results:** The system displays the Generate Disclosures section with all available fields.  
**Requirement Mapping:** NA  

---

#### Step 04  
**Test Step Description:** Validate the visibility of the "Intent to Proceed" checkbox field.  
**Screen Name:** Generate Disclosures  
**Test Data:** NA  
**Expected Results:** The system displays the "Intent to Proceed" checkbox field with options: Checked and Unchecked.  
**Requirement Mapping:** 718521_AC_01  

---

#### Step 05  
**Test Step Description:** Validate the functionality of the "Intent to Proceed" checkbox field by selecting "Checked".  
**Screen Name:** Generate Disclosures  
**Test Data:** Checked  
**Expected Results:** The system updates the field value to "Checked" and saves the selection successfully.  
**Requirement Mapping:** 718521_AC_01  

---

#### Step 06  
**Test Step Description:** Validate the functionality of the "Intent to Proceed" checkbox field by selecting "Unchecked".  
**Screen Name:** Generate Disclosures  
**Test Data:** Unchecked  
**Expected Results:** The system updates the field value to "Unchecked" and saves the selection successfully.  
**Requirement Mapping:** 718521_AC_01  

---

#### Step 07  
**Test Step Description:** Validate the visibility of the "Higher Priced Mortgage Loan" dropdown field.  
**Screen Name:** Generate Disclosures  
**Test Data:** NA  
**Expected Results:** The system displays the "Higher Priced Mortgage Loan" dropdown field with options: Select..., Yes, No.  
**Requirement Mapping:** 718521_AC_02  

---

#### Step 08  
**Test Step Description:** Validate the functionality of the "Higher Priced Mortgage Loan" dropdown field by selecting "Yes".  
**Screen Name:** Generate Disclosures  
**Test Data:** Yes  
**Expected Results:** The system updates the field value to "Yes" and saves the selection successfully.  
**Requirement Mapping:** 718521_AC_02  

---

#### Step 09  
**Test Step Description:** Validate the functionality of the "Higher Priced Mortgage Loan" dropdown field by selecting "No".  
**Screen Name:** Generate Disclosures  
**Test Data:** No  
**Expected Results:** The system updates the field value to "No" and saves the selection successfully.  
**Requirement Mapping:** 718521_AC_02  

---

#### Step 10  
**Test Step Description:** Validate the visibility of the "HPML DV Override" checkbox field.  
**Screen Name:** Generate Disclosures  
**Test Data:** NA  
**Expected Results:** The system displays the "HPML DV Override" checkbox field with options: Checked and Unchecked.  
**Requirement Mapping:** 718521_AC_03  

---

#### Step 11  
**Test Step Description:** Validate the functionality of the "HPML DV Override" checkbox field by selecting "Checked".  
**Screen Name:** Generate Disclosures  
**Test Data:** Checked  
**Expected Results:** The system updates the field value to "Checked" and saves the selection successfully.  
**Requirement Mapping:** 718521_AC_03  

---

#### Step 12  
**Test Step Description:** Validate the functionality of the "HPML DV Override" checkbox field by selecting "Unchecked".  
**Screen Name:** Generate Disclosures  
**Test Data:** Unchecked  
**Expected Results:** The system updates the field value to "Unchecked" and saves the selection successfully.  
**Requirement Mapping:** 718521_AC_03  

---

#### Step 13  
**Screen Name:** Generate Disclosures  
**Test Data:** NA  
**Requirement Mapping:** NA  

---

#### Step 14  
**Test Step Description:** Log out from H2O-A.  
**Screen Name:** Application Header  
**Test Data:** NA  
**Expected Results:** The system terminates the session and redirects to the login page.  
**Requirement Mapping:** NA  

---  

### End of Test Case
