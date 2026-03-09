### Test Case ID / Test Script ID: 718521_RTL_01  
### Test Scenario Id: 718521_SC_01  
### Test Scenario Description: Validate the addition of new fields in the Modernized Audit section under DIS > Generate Disclosures.  
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
**Screen Name:** Dashboard > DIS > Generate Disclosures  
**Test Data:** NA  
**Expected Results:** The system displays the Generate Disclosures section with all available fields.  
**Requirement Mapping:** 718521_AC_01  

---

#### Step 04  
**Test Step Description:** Validate the visibility of the "Intent to Proceed" checkbox field.  
**Screen Name:** Generate Disclosures  
**Test Data:** NA  
**Expected Results:** The system displays the "Intent to Proceed" checkbox field with values: Checked, Unchecked.  
**Requirement Mapping:** 718521_AC_02  

---

#### Step 05  
**Test Step Description:** Validate the functionality of the "Intent to Proceed" checkbox field by selecting "Checked".  
**Screen Name:** Generate Disclosures  
**Test Data:** Checked  
**Expected Results:** The system updates the field value to "Checked" and saves the selection successfully.  
**Requirement Mapping:** 718521_AC_02  

---

#### Step 06  
**Test Step Description:** Validate the functionality of the "Intent to Proceed" checkbox field by selecting "Unchecked".  
**Screen Name:** Generate Disclosures  
**Test Data:** Unchecked  
**Expected Results:** The system updates the field value to "Unchecked" and saves the selection successfully.  
**Requirement Mapping:** 718521_AC_02  

---

#### Step 07  
**Test Step Description:** Validate the visibility of the "Higher Priced Mortgage Loan" dropdown field.  
**Screen Name:** Generate Disclosures  
**Test Data:** NA  
**Expected Results:** The system displays the "Higher Priced Mortgage Loan" dropdown field with values: Select..., Yes, No.  
**Requirement Mapping:** 718521_AC_03  

---

#### Step 08  
**Test Step Description:** Validate the functionality of the "Higher Priced Mortgage Loan" dropdown field by selecting "Yes".  
**Screen Name:** Generate Disclosures  
**Test Data:** Yes  
**Expected Results:** The system updates the field value to "Yes" and saves the selection successfully.  
**Requirement Mapping:** 718521_AC_03  

---

#### Step 09  
**Test Step Description:** Validate the functionality of the "Higher Priced Mortgage Loan" dropdown field by selecting "No".  
**Screen Name:** Generate Disclosures  
**Test Data:** No  
**Expected Results:** The system updates the field value to "No" and saves the selection successfully.  
**Requirement Mapping:** 718521_AC_03  

---

#### Step 10  
**Test Step Description:** Validate the visibility of the "HPML DV Override" checkbox field.  
**Screen Name:** Generate Disclosures  
**Test Data:** NA  
**Expected Results:** The system displays the "HPML DV Override" checkbox field with values: Checked, Unchecked.  
**Requirement Mapping:** 718521_AC_04  

---

#### Step 11  
**Test Step Description:** Validate the functionality of the "HPML DV Override" checkbox field by selecting "Checked".  
**Screen Name:** Generate Disclosures  
**Test Data:** Checked  
**Expected Results:** The system updates the field value to "Checked" and saves the selection successfully.  
**Requirement Mapping:** 718521_AC_04  

---

#### Step 12  
**Test Step Description:** Validate the functionality of the "HPML DV Override" checkbox field by selecting "Unchecked".  
**Screen Name:** Generate Disclosures  
**Test Data:** Unchecked  
**Expected Results:** The system updates the field value to "Unchecked" and saves the selection successfully.  
**Requirement Mapping:** 718521_AC_04  

---

#### Step 13  
**Screen Name:** Generate Disclosures  
**Test Data:** NA  
**Requirement Mapping:** 718521_AC_05  

---

#### Step 14  
**Test Step Description:** Log out from H2O-A.  
**Screen Name:** Application Header  
**Test Data:** NA  
**Expected Results:** The system terminates the session and redirects to the login page.  
**Requirement Mapping:** NA  

---

### Notes:  
- Privilege restrictions are validated implicitly by ensuring the absence of broker-related fields in the UI.  
- All validations align strictly with the acceptance criteria and channel rules.
=================================================================================================
### Test Case: 718521_WHL_01  
**Test Scenario ID:** 718521_SC_01  
**Test Scenario Description:** Validate the addition of new fields in the Modernized Audit section under DIS > Generate Disclosures.  

**Test Script Description:** This test script validates the presence, functionality, and business rules of the newly added fields in the Modernized Audit section under DIS > Generate Disclosures. It includes positive and negative scenarios for field interactions and privilege restrictions.  

**Pre-Condition & Assumptions:**  
Refer to provided precondition context:  
1. Channel: Wholesale  
2. Loan Type: Conventional  
3. Product Code: CF30  
4. Branch: Any CE branch  
5. Loan stage: Created  
6. Loan should not be locked  

---

### Test Steps  

**Step 01** | Log in to H2O-A in UAT environment | Login | Valid UAT credentials | The system authenticates the user and displays the dashboard | NA  

**Step 02** | Open the loan created as per precondition | Loan Summary | Loan Number from precondition | The system loads the loan in editable state | NA  

**Step 03** | Navigate to DIS > Generate Disclosures | Left Navigation Panel | NA | The system displays the Generate Disclosures section | NA  

**Step 04** | Validate the presence of the "Intent to Proceed" checkbox | Generate Disclosures | NA | The system displays the "Intent to Proceed" checkbox with values: Checked, Unchecked | 718521_AC_01  

**Step 05** | Check the "Intent to Proceed" checkbox | Generate Disclosures | NA | The system marks the checkbox as Checked | 718521_AC_01  

**Step 06** | Uncheck the "Intent to Proceed" checkbox | Generate Disclosures | NA | The system marks the checkbox as Unchecked | 718521_AC_01  

**Step 07** | Validate the presence of the "Higher Priced Mortgage Loan" dropdown | Generate Disclosures | NA | The system displays the "Higher Priced Mortgage Loan" dropdown with values: Select..., Yes, No | 718521_AC_02  

**Step 08** | Select "Yes" from the "Higher Priced Mortgage Loan" dropdown | Generate Disclosures | NA | The system updates the field value to Yes | 718521_AC_02  

**Step 09** | Select "No" from the "Higher Priced Mortgage Loan" dropdown | Generate Disclosures | NA | The system updates the field value to No | 718521_AC_02  

**Step 10** | Validate the presence of the "HPML DV Override" checkbox | Generate Disclosures | NA | The system displays the "HPML DV Override" checkbox with values: Checked, Unchecked | 718521_AC_03  

**Step 11** | Check the "HPML DV Override" checkbox | Generate Disclosures | NA | The system marks the checkbox as Checked | 718521_AC_03  

**Step 12** | Uncheck the "HPML DV Override" checkbox | Generate Disclosures | NA | The system marks the checkbox as Unchecked | 718521_AC_03  

**Step 13** | Validate the presence of the "Mortgage Broker Fee Agreement" dropdown | Mortgage Broker Fee/Compensation Agreement | NA | The system displays the "Mortgage Broker Fee Agreement" dropdown with values: Select..., Yes, No | 718521_AC_04  

**Step 14** | Select "Yes" from the "Mortgage Broker Fee Agreement" dropdown | Mortgage Broker Fee/Compensation Agreement | NA | The system updates the field value to Yes | 718521_AC_04  

**Step 15** | Select "No" from the "Mortgage Broker Fee Agreement" dropdown | Mortgage Broker Fee/Compensation Agreement | NA | The system updates the field value to No | 718521_AC_04  

**Step 16** | Validate the presence of the "Mortgage Broker License Type" dropdown | Mortgage Broker Fee/Compensation Agreement | NA | The system displays the "Mortgage Broker License Type" dropdown with values: Select..., CFL, DRE, RML | 718521_AC_05  

**Step 17** | Select "CFL" from the "Mortgage Broker License Type" dropdown | Mortgage Broker Fee/Compensation Agreement | NA | The system updates the field value to CFL | 718521_AC_05  

**Step 18** | Select "DRE" from the "Mortgage Broker License Type" dropdown | Mortgage Broker Fee/Compensation Agreement | NA | The system updates the field value to DRE | 718521_AC_05  

**Step 19** | Select "RML" from the "Mortgage Broker License Type" dropdown | Mortgage Broker Fee/Compensation Agreement | NA | The system updates the field value to RML | 718521_AC_05  

**Step 20** | Validate privilege restrictions for "Mortgage Broker Fee Agreement" and "Mortgage Broker License Type" fields | Mortgage Broker Fee/Compensation Agreement | NA | The system restricts access to these fields based on user privileges | 718521_AC_06  

**Step 21** | Validate the visibility of "Mortgage Broker License Type" when SubPropState = CA | Mortgage Broker Fee/Compensation Agreement | SubPropState = CA | The system displays the "Mortgage Broker License Type" dropdown when SubPropState = CA | 718521_AC_07  

**Step 22** | Log out from H2O-A | Application Header | NA | The system terminates the session and redirects to the login page | NA  

---

### Notes:  
- Privilege restrictions for "Mortgage Broker Fee Agreement" and "Mortgage Broker License Type" must be validated using appropriate user roles.  
- SubPropState = CA logic must be confirmed with development for exact implementation.
