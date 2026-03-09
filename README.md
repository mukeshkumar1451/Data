=====================================
ADO INTELLIGENCE ANALYSIS OUTPUT
=====================================


Story ID: 718521
Title: Modernized Audit additions - DIS > Generate Disclosures Fields
Timestamp: 20260309_122419
------------ DESCRIPTION ------------
Business would like to add the following fields to Modernized Audit. Description H2O UI Location HPML DIS > Generate Disclosures > Generate Disclosure Intent to Proceed DIS > Generate Disclosures Mortgage Broker Fee Agreement DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement Mortgage Broker License Type DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement HPML - Intent to Proceed - Mortgage Broker Fee/Compensation Agreement - *Appears to be privilege restricted Mortgage Broker License Type - *Unsure of exact logic to get this license section to appear but it looks like it is appears when SubPropState = CA. Dev to advise of logic. **Also appears to be privilege restricted
------ ACCEPTANCE CRITERIA ----------
Business would like to add the following fields to Modernized Audit.DescriptionH2O UI LocationHPMLDIS > Generate Disclosures > Generate DisclosureIntent to ProceedDIS > Generate DisclosuresMortgage Broker Fee AgreementDIS > Generate Disclosures > Mortgage Broker Fee/Compensation AgreementMortgage Broker License TypeDIS > Generate Disclosures > Mortgage Broker Fee/Compensation AgreementHPML -Intent to Proceed -Mortgage Broker Fee/Compensation Agreement -*Appears to be privilege restrictedMortgage Broker License Type -*Unsure of exact logic to get this license section to appear but it looks like it is appears when SubPropState = CA. Dev to advise of logic.**Also appears to be privilege restricted

Business would like to add the following fields to Modernized Audit.

DescriptionH2O UI LocationHPMLDIS > Generate Disclosures > Generate DisclosureIntent to ProceedDIS > Generate DisclosuresMortgage Broker Fee AgreementDIS > Generate Disclosures > Mortgage Broker Fee/Compensation AgreementMortgage Broker License TypeDIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement

HPML -

[Image Analysis]
Section:  
Generate Disclosure  

Fields:  
- Intent to Proceed (Checkbox)  
- Allow Appraisal Order (Checkbox)  
- Bypass Compliance Check (Checkbox)  
- Ignore 3rd Party Fee Check (Checkbox)  
- Title Fees Verified after LA Increase (Checkbox)  
- Ignore Fee Quote Data Validations (Checkbox)  
- Higher Priced Mortgage Loan (Dropdown)  
- HPML DV Override (Checkbox)  

Buttons:  
- Send Via (Dropdown)  

Values:  
- Higher Priced Mortgage Loan: Select..., Yes, No  
- Send Via: eSign  

Intent to Proceed -

[Image Analysis]
Section:  
Generate Disclosure  

Fields:  
- Intent to Proceed (Checkbox)  

Buttons:  
None  

Values:  
None  

Mortgage Broker Fee/Compensation Agreement -

[Image Analysis]
Section:  
Mortgage Broker Fee/Compensation Agreement  

Fields:  
- Do you want to include Mortgage Broker Fee/Compensation Agreement in the Newrez LE Package?  

Buttons:  
- Dropdown menu (Select, Yes, No)  

Values:  
- Select  
- Yes  
- No  

Section:  
Manage Additional Broker Disclosures  

Fields:  
- This functionality provides the ability to:  
  - Append additional disclosures to the Newrez LE Package  

Buttons:  
- Manage Broker Disclosures  

*Appears to be privilege restricted

Mortgage Broker License Type -

[Image Analysis]
Section:  
Mortgage Broker Fee/Compensation Agreement  

Fields:  
- Do you want to include Mortgage Broker Fee/Compensation Agreement in the Newrez LE Package? (Dropdown: Yes/No)  
- Under which license will you originate this loan? (Dropdown: Select..., CFL, DRE, RML)  

Buttons:  
- None  

Values:  
- Disclosures to be appended to Newrez LE Package: 0  

Section:  
Manage Additional Broker Disclosures  

Fields:  
- None  

Buttons:  
- Manage Broker Disclosures  

Values:  
- None  

*Unsure of exact logic to get this license section to appear but it looks like it is appears when SubPropState = CA. Dev to advise of logic.

**Also appears to be privilege restricted

