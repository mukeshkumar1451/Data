user_story_id: "7123445"

user_story: |
  Modernized Audit additions - DIS > Generate Disclosures Fields

description: |
  Business wants additional fields from DIS > Generate Disclosures screens
  to be captured and displayed in Modernized Audit.

acceptance_criteria: |
  AC1: Capture HPML field in Modernized Audit

  Given the user navigates to DIS > Generate Disclosures > Generate Disclosure screen
  When HPML field is present
  Then the HPML value should be captured in Modernized Audit

  AC2: Capture Intent to Proceed in Modernized Audit

  Given the user navigates to DIS > Generate Disclosures screen
  When Intent to Proceed value is available
  Then the value should be recorded in Modernized Audit

  AC3: Capture Mortgage Broker Fee Agreement details

  Given the user opens DIS > Generate Disclosures > Mortgage Broker Fee/Compensation Agreement
  When the agreement details are visible
  Then those values should be captured in Modernized Audit

  AC4: Capture Mortgage Broker License Type when SubPropState = CA

  Given SubPropState is CA
  And the user opens Mortgage Broker Fee/Compensation Agreement screen
  When Mortgage Broker License Type section appears
  Then the license type should be captured in Modernized Audit
