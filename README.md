=========== ADO INTELLIGENCE AGENT OUTPUT ===========

User Story ID:
718521

----------- TITLE -----------
Modernized Audit additions - DIS > Generate Disclosures Fields

----------- ENRICHED DESCRIPTION -----------
1. User can select "Generate Disclosure" button.  
2. User can select "Intent to Proceed" checkbox.  
3. User can select "Mortgage Broker Fee/Compensation Agreement" checkbox.  
4. If "Mortgage Broker Fee/Compensation Agreement" is selected, system validates privilege restrictions.  
5. If "SubPropState" is set to "CA," "Mortgage Broker License Type" field appears.  
6. If "Mortgage Broker License Type" field appears, system validates privilege restrictions.  
7. User can select "Do you want to include Mortgage Broker Fee/Compensation Agreement in the Newrez LE Package?" dropdown.  
8. System validates that the "Mortgage Broker Fee/Compensation Agreement" form meets license/registration disclosure requirements before inclusion.  
9. User can select "Under which license will you originate this loan?" dropdown.  
10. User can select "Higher Priced Mortgage Loan" dropdown.  
11. User can select "Send via" dropdown.  
12. User can toggle "Allow appraisal order" field.  
13. User can toggle "Bypass compliance check" field.  
14. User can toggle "Ignore 3rd Party Fee Check" field.  
15. User can toggle "Title Fees Verified after LA Increase" field.  
16. User can toggle "Ignore Fee Quote Data validations" field.  
17. System validates "Electronic Delivery" selection.  
18. User can toggle "NPM PV Override" field.

----------- CHANNELS -----------
['RTL', 'WHL', 'DTC', 'CL1']

----------- ENRICHED ACCEPTANCE CRITERIA -----------
1. User can select "Generate Disclosure" button.  
2. User can select "Intent to Proceed" checkbox.  
3. User can select "Mortgage Broker Fee/Compensation Agreement" checkbox.  
4. If "SubPropState" is set to "CA", "Mortgage Broker License Type" field appears.  
5. System validates if the user has privilege access to view "Mortgage Broker Fee/Compensation Agreement".  
6. System validates if the user has privilege access to view "Mortgage Broker License Type".  
7. User can select "Do you want to include Mortgage Broker Fee/Compensation Agreement in the Newrez LE Package?" dropdown.  
8. User can select "Under which license will you originate this loan?" dropdown.  
9. User can select "Higher Priced Mortgage Loan" dropdown.  
10. User can select "Send via" dropdown.  
11. User can toggle "Allow appraisal order" field.  
12. User can toggle "Bypass compliance check" field.  
13. User can toggle "Ignore 3rd Party Fee Check" field.  
14. User can toggle "Title Fees Verified after LA Increase" field.  
15. User can toggle "Ignore Fee Quote Data validations" field.  
16. System validates if disclosures are sent electronically when consent is provided.  
17. System validates if disclosures are sent via mail when consent is not provided.

