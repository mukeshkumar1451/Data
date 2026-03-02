=====================================
ADO INTELLIGENCE ANALYSIS OUTPUT
=====================================


Story ID: 718521
Title: Modernized Audit additions - DIS > Generate Disclosures Fields
Timestamp: 20260302_120649
------------ DESCRIPTION ------------
Business would like to add the following fields to Modernized Audit. 
 
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
------ ACCEPTANCE CRITERIA ----------
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

Mortgage Broker Fee/Compensation Agreement
Navigate to DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement.
Verify that the "Mortgage Broker Fee/Compensation Agreement" field is rendered as a Dropdown.
Verify that the "Mortgage Broker Fee/Compensation Agreement" dropdown contains exactly the following options:
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
