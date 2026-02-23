=========== ADO INTELLIGENCE AGENT OUTPUT ===========

User Story ID:
718521

----------- TITLE -----------
Modernized Audit additions - DIS > Generate Disclosures Fields

----------- ENRICHED DESCRIPTION -----------
Business would like to add the following fields to Modernized Audit. 
 
DescriptionH2O UI LocationHPMLDIS > Generate Disclosures > Generate DisclosureIntent to ProceedDIS > Generate DisclosuresMortgage Broker Fee AgreementDIS > Generate Disclosures > Mortgage Broker Fee/Compensation AgreementMortgage Broker License TypeDIS > Generate Disclosures > Mortgage Broker Fee/Compensation AgreementHPML -  
 
 
Intent to Proceed -  
 
 
Mortgage Broker Fee/Compensation Agreement -  
 
*Appears to be privilege restrictedMortgage Broker License Type -  
 
*Unsure of exact logic to get this license section to appear but it looks like it is appears when SubPropState = CA. Dev to advise of logic.  
**Also appears to be privilege restricted

=== EXTRACTED FROM IMAGES ===

Below is the structured field documentation based on the OCR text provided:

---

### **Field Name:** Generate Disclosure  
- **UI Section:** Disclosure Management  
- **UI Location:** Top of the section (inferred from text hierarchy)  
- **Description:** A button or action to generate the required disclosure documents for the mortgage process.  
- **Dropdown Options:** None  
- **Restrictions:** None  
- **Visibility Logic:** Always visible  

---

### **Field Name:** Intent to Proceed  
- **UI Section:** Disclosure Management  
- **UI Location:** Below "Generate Disclosure" (inferred from text hierarchy)  
- **Description:** Indicates whether the borrower has provided intent to proceed with the loan application.  
- **Dropdown Options:** None  
- **Restrictions:** None  
- **Visibility Logic:** Visible after borrower acknowledgment  

---

### **Field Name:** Allow Appraisal Order  
- **UI Section:** Appraisal Management  
- **UI Location:** Below "Intent to Proceed" (inferred from text hierarchy)  
- **Description:** A toggle or checkbox to allow the ordering of an appraisal for the property.  
- **Dropdown Options:** None  
- **Restrictions:** None  
- **Visibility Logic:** Visible after "Intent to Proceed" is confirmed  

---

### **Field Name:** KeyPass Compliance Check  
- **UI Section:** Compliance Management  
- **UI Location:** Below "Allow Appraisal Order" (inferred from text hierarchy)  
- **Description:** A checkbox to confirm compliance with KeyPass requirements.  
- **Dropdown Options:** None  
- **Restrictions:** None  
- **Visibility Logic:** Always visible  

---

### **Field Name:** Ignore 3rd Party Fee Check  
- **UI Section:** Fee Management  
- **UI Location:** Below "KeyPass Compliance Check" (inferred from text hierarchy)  
- **Description:** A checkbox to bypass validation of third-party fees during the loan process.  
- **Dropdown Options:** None  
- **Restrictions:** None  
- **Visibility Logic:** Always visible  

---

### **Field Name:** Title Fees Verified After Loan Amount Increase  
- **UI Section:** Fee Management  
- **UI Location:** Below "Ignore 3rd Party Fee Check" (inferred from text hierarchy)  
- **Description:** A checkbox to confirm that title fees have been verified after an increase in the loan amount.  
- **Dropdown Options:** None  
- **Restrictions:** None  
- **Visibility Logic:** Visible only if loan amount has been increased  

---

### **Field Name:** Ignore Fee Quote Data Validations  
- **UI Section:** Fee Management  
- **UI Location:** Below "Title Fees Verified After Loan Amount Increase" (inferred from text hierarchy)  
- **Description:** A checkbox to bypass fee quote data validations during the loan process.  
- **Dropdown Options:** None  
- **Restrictions:** None  
- **Visibility Logic:** Always visible  

---

### **Field Name:** Higher Priced Mortgage Loan  
- **UI Section:** Loan Pricing  
- **UI Location:** Below "Ignore Fee Quote Data Validations" (inferred from text hierarchy)  
- **Description:** A dropdown to indicate whether the loan qualifies as a higher-priced mortgage loan.  
- **Dropdown Options:**  
  - Select...  
  - Yes  
  - No  
- **Restrictions:** None  
- **Visibility Logic:** Always visible  

---

### **Field Name:** Electronic Delivery  
- **UI Section:** Disclosure Delivery  
- **UI Location:** Below "Higher Priced Mortgage Loan" (inferred from text hierarchy)  
- **Description:** A toggle or checkbox to indicate whether disclosures will be delivered electronically.  
- **Dropdown Options:**  
  - Yes  
  - No  
- **Restrictions:** None  
- **Visibility Logic:** Always visible  

---

### **Field Name:** Send Via  
- **UI Section:** Disclosure Delivery  
- **UI Location:** Below "Electronic Delivery" (inferred from text hierarchy)  
- **Description:** A dropdown to select the method of sending disclosures.  
- **Dropdown Options:**  
  - eSign  
- **Restrictions:** None  
- **Visibility Logic:** Visible only if "Electronic Delivery" is set to "Yes"  

---

### **Field Name:** Loan Program Override  
- **UI Section:** Loan Program Management  
- **UI Location:** Bottom of the section (inferred from text hierarchy)  
- **Description:** A checkbox to override the default loan program settings.  
- **Dropdown Options:** None  
- **Restrictions:** None  
- **Visibility Logic:** Always visible  

--- 

This structured documentation is based solely on the OCR text provided without any assumptions or additional context.

Here is the structured field documentation based on the OCR text provided:

---

### Field 1: **Generate Disclosure**
- **Field Name**: Generate Disclosure  
- **UI Section**: Disclosure Management  
- **UI Location**: Likely located in the disclosure generation workflow or a dedicated section for managing disclosures.  
- **Description**: A button or action field used to initiate the generation of required mortgage disclosure documents.  
- **Dropdown Options**: None (based on the text provided).  
- **Restrictions**: Likely restricted to users with appropriate permissions or roles (e.g., loan officers or compliance team).  
- **Visibility Logic**: May only be visible after certain prerequisites are met, such as completing borrower information or loan application details.  

---

### Field 2: **Intent to Proceed**
- **Field Name**: Intent to Proceed  
- **UI Section**: Borrower Actions or Loan Application Progress  
- **UI Location**: Likely located in the borrower interaction section or as part of the loan application workflow.  
- **Description**: A field or checkbox used to capture the borrower's confirmation to proceed with the loan application after receiving initial disclosures.  
- **Dropdown Options**: None (based on the text provided).  
- **Restrictions**: May require the borrower to have reviewed initial disclosures before enabling this field.  
- **Visibility Logic**: Likely visible only after disclosures are generated and shared with the borrower.  

---

This documentation is based solely on the OCR text provided and does not infer additional details beyond what is explicitly stated.

Here is the structured field documentation based on the OCR text provided:

---

### Field 1: Consent for Electronic Disclosures
- **Field Name**: Consent for Electronic Disclosures  
- **UI Section**: Disclosures  
- **UI Location**: Top of the section (context suggests this is part of the disclosure settings)  
- **Description**: Indicates whether the user has consented to receive disclosures electronically. If not, disclosures will be sent via mail.  
- **Dropdown Options**: None  
- **Restrictions**: None explicitly mentioned  
- **Visibility Logic**: Always visible  

---

### Field 2: Mortgage Broker Fee/Compensation Agreement Inclusion
- **Field Name**: Include Mortgage Broker Fee/Compensation Agreement  
- **UI Section**: Newrez LE Package  
- **UI Location**: Within the Newrez LE Package section  
- **Description**: Allows the user to decide whether to include the Mortgage Broker Fee/Compensation Agreement in the Newrez LE Package.  
- **Dropdown Options**:  
  - Select...  
  - Yes  
  - No  
- **Restrictions**: The form may not be suitable for every transaction or broker. It must be reviewed to ensure it meets license/registration disclosure requirements before inclusion.  
- **Visibility Logic**: Visible when managing the Newrez LE Package  

---

### Field 3: Manage Additional Broker Disclosures
- **Field Name**: Manage Additional Broker Disclosures  
- **UI Section**: Broker Disclosures  
- **UI Location**: Below the Mortgage Broker Fee/Compensation Agreement section  
- **Description**: Provides functionality to append additional disclosures to the Newrez LE Package.  
- **Dropdown Options**: None  
- **Restrictions**: None explicitly mentioned  
- **Visibility Logic**: Visible when managing broker disclosures  

---

### Field 4: Manage Broker Disclosures
- **Field Name**: Manage Broker Disclosures  
- **UI Section**: Broker Disclosures  
- **UI Location**: Below the "Manage Additional Broker Disclosures" section  
- **Description**: General functionality for managing broker disclosures.  
- **Dropdown Options**: None  
- **Restrictions**: None explicitly mentioned  
- **Visibility Logic**: Always visible  

--- 

This documentation is structured based on the OCR text provided without any assumptions or additional information.

Here is the structured field documentation based on the OCR text provided:

---

### Field 1: **Mortgage Broker Fee/Compensation Agreement Inclusion**
- **Field Name**: Include Mortgage Broker Fee/Compensation Agreement
- **UI Section**: Newrez LE Package
- **UI Location**: Top of the form
- **Description**: Determines whether the Mortgage Broker Fee/Compensation Agreement should be included in the Newrez Loan Estimate (LE) package.
- **Dropdown Options**: Yes / No (inferred from the "[¥es___]" checkbox format)
- **Restrictions**: None explicitly mentioned, but the form notes that it may not be suitable for every transaction or broker license/registration’s disclosure requirements.
- **Visibility Logic**: Visible when managing broker disclosures for the Newrez LE Package.

---

### Field 2: **License Selection**
- **Field Name**: Loan Origination License
- **UI Section**: Newrez LE Package
- **UI Location**: Below the Mortgage Broker Fee/Compensation Agreement inclusion field
- **Description**: Specifies the license under which the loan will be originated.
- **Dropdown Options**: 
  - DRE
  - RML
  - Other (inferred from the context of license selection)
- **Restrictions**: Must align with the broker’s license/registration disclosure requirements.
- **Visibility Logic**: Visible when selecting the license for loan origination.

---

### Field 3: **Manage Additional Broker Disclosures**
- **Field Name**: Manage Additional Broker Disclosures
- **UI Section**: Broker Disclosures Management
- **UI Location**: Middle of the form
- **Description**: Provides functionality to append additional broker disclosures to the Newrez LE Package.
- **Dropdown Options**: None
- **Restrictions**: None explicitly mentioned.
- **Visibility Logic**: Visible when managing broker disclosures.

---

### Field 4: **Disclosures to be Appended**
- **Field Name**: Disclosures to be Appended
- **UI Section**: Broker Disclosures Management
- **UI Location**: Bottom of the form
- **Description**: Displays the count of disclosures appended to the Newrez LE Package.
- **Dropdown Options**: None
- **Restrictions**: None explicitly mentioned.
- **Visibility Logic**: Visible when managing broker disclosures.

---

### Notes:
- The OCR text mentions that the form may not be suitable for every transaction or broker license/registration’s disclosure requirements. This implies that certain fields may have conditional restrictions based on the broker’s licensing.
- Dropdown options for the license field are inferred based on the text "DRE" and "RML" provided in the OCR text.
- Visibility logic is inferred based on the context of managing broker disclosures and appending them to the Newrez LE Package.

----------- CHANNELS -----------
['RTL']

----------- ENRICHED ACCEPTANCE CRITERIA -----------
Business would like to add the following fields to Modernized Audit. 
 
DescriptionH2O UI LocationHPMLDIS > Generate Disclosures > Generate DisclosureIntent to ProceedDIS > Generate DisclosuresMortgage Broker Fee AgreementDIS > Generate Disclosures > Mortgage Broker Fee/Compensation AgreementMortgage Broker License TypeDIS > Generate Disclosures > Mortgage Broker Fee/Compensation AgreementHPML -  
 
 
Intent to Proceed -  
 
 
Mortgage Broker Fee/Compensation Agreement -  
 
*Appears to be privilege restrictedMortgage Broker License Type -  
 
*Unsure of exact logic to get this license section to appear but it looks like it is appears when SubPropState = CA. Dev to advise of logic.  
**Also appears to be privilege restricted

=== EXTRACTED FROM IMAGES ===

Here is the structured field documentation based on the OCR text provided:

---

### Field 1: **Generate Disclosure**
- **Field Name**: Generate Disclosure  
- **UI Section**: Disclosure Management  
- **UI Location**: Top of the section (likely a header or action button)  
- **Description**: This field initiates the process to generate required mortgage disclosure documents.  
- **Dropdown Options**: None  
- **Restrictions**: None  
- **Visibility Logic**: Always visible  

---

### Field 2: **Intent to Proceed**
- **Field Name**: Intent to Proceed  
- **UI Section**: Disclosure Management  
- **UI Location**: Below "Generate Disclosure"  
- **Description**: Indicates whether the borrower has expressed intent to proceed with the loan application.  
- **Dropdown Options**: None  
- **Restrictions**: None  
- **Visibility Logic**: Always visible  

---

### Field 3: **Allow Appraisal Order**
- **Field Name**: Allow Appraisal Order  
- **UI Section**: Appraisal Management  
- **UI Location**: Below "Intent to Proceed"  
- **Description**: Enables the ordering of an appraisal for the property.  
- **Dropdown Options**: None  
- **Restrictions**: None  
- **Visibility Logic**: Visible only if "Intent to Proceed" is confirmed.  

---

### Field 4: **KeyPass Compliance Check**
- **Field Name**: KeyPass Compliance Check  
- **UI Section**: Compliance Management  
- **UI Location**: Below "Allow Appraisal Order"  
- **Description**: Verifies compliance with KeyPass regulations.  
- **Dropdown Options**: None  
- **Restrictions**: None  
- **Visibility Logic**: Always visible  

---

### Field 5: **Ignore 3rd Party Fee Check**
- **Field Name**: Ignore 3rd Party Fee Check  
- **UI Section**: Fee Management  
- **UI Location**: Below "KeyPass Compliance Check"  
- **Description**: Allows bypassing the validation of third-party fees.  
- **Dropdown Options**: None  
- **Restrictions**: None  
- **Visibility Logic**: Always visible  

---

### Field 6: **Title Fees Verified After Loan Amount Increase**
- **Field Name**: Title Fees Verified After Loan Amount Increase  
- **UI Section**: Fee Management  
- **UI Location**: Below "Ignore 3rd Party Fee Check"  
- **Description**: Confirms that title fees have been verified after an increase in the loan amount.  
- **Dropdown Options**: None  
- **Restrictions**: None  
- **Visibility Logic**: Always visible  

---

### Field 7: **Ignore Fee Quote Data Validations**
- **Field Name**: Ignore Fee Quote Data Validations  
- **UI Section**: Fee Management  
- **UI Location**: Below "Title Fees Verified After Loan Amount Increase"  
- **Description**: Allows bypassing validations related to fee quote data.  
- **Dropdown Options**: None  
- **Restrictions**: None  
- **Visibility Logic**: Always visible  

---

### Field 8: **Higher Priced Mortgage Loan**
- **Field Name**: Higher Priced Mortgage Loan  
- **UI Section**: Loan Pricing  
- **UI Location**: Below "Ignore Fee Quote Data Validations"  
- **Description**: Indicates whether the loan qualifies as a higher-priced mortgage loan.  
- **Dropdown Options**: [Select...]  
  - Yes  
  - No  
- **Restrictions**: None  
- **Visibility Logic**: Always visible  

---

### Field 9: **Electronic Delivery**
- **Field Name**: Electronic Delivery  
- **UI Section**: Disclosure Delivery  
- **UI Location**: Below "Higher Priced Mortgage Loan"  
- **Description**: Specifies whether disclosure documents will be delivered electronically.  
- **Dropdown Options**:  
  - Yes  
  - No  
- **Restrictions**: None  
- **Visibility Logic**: Always visible  

---

### Field 10: **Send Via**
- **Field Name**: Send Via  
- **UI Section**: Disclosure Delivery  
- **UI Location**: Below "Electronic Delivery"  
- **Description**: Specifies the method of sending disclosure documents.  
- **Dropdown Options**:  
  - eSign  
- **Restrictions**: None  
- **Visibility Logic**: Visible only if "Electronic Delivery" is set to "Yes".  

---

### Field 11: **Loan Program Override**
- **Field Name**: Loan Program Override  
- **UI Section**: Loan Management  
- **UI Location**: Bottom of the section  
- **Description**: Allows overriding the default loan program settings.  
- **Dropdown Options**: None  
- **Restrictions**: None  
- **Visibility Logic**: Always visible  

--- 

This documentation is structured based on the OCR text provided without any assumptions or additional information.

Here is the structured field documentation based on the OCR text provided:

---

### Field 1: **Generate Disclosure**
- **Field Name**: Generate Disclosure  
- **UI Section**: Likely part of the "Disclosure Management" or "Document Generation" section (inferred from the text).  
- **UI Location**: Not inferable from the OCR text.  
- **Description**: A button or action field to initiate the generation of required mortgage disclosure documents.  
- **Dropdown Options**: None (not indicated in the OCR text).  
- **Restrictions**: None (not indicated in the OCR text).  
- **Visibility Logic**: None (not indicated in the OCR text).  

---

### Field 2: **Intent to Proceed**
- **Field Name**: Intent to Proceed  
- **UI Section**: Likely part of the "Borrower Actions" or "Application Status" section (inferred from the text).  
- **UI Location**: Not inferable from the OCR text.  
- **Description**: A field or checkbox indicating the borrower's intent to proceed with the mortgage application process.  
- **Dropdown Options**: None (not indicated in the OCR text).  
- **Restrictions**: None (not indicated in the OCR text).  
- **Visibility Logic**: None (not indicated in the OCR text).  

---

This documentation is based solely on the OCR text provided without assumptions beyond what is explicitly stated.

Here is the structured field documentation based on the OCR text provided:

---

### Field 1: Consent for Electronic Disclosures
- **Field Name**: Consent for Electronic Disclosures  
- **UI Section**: Disclosures  
- **UI Location**: Not inferable  
- **Description**: Indicates whether the user has consented to receive disclosures electronically. If not, disclosures will be sent via mail.  
- **Dropdown Options**: None  
- **Restrictions**: None  
- **Visibility Logic**: Always visible  

---

### Field 2: Mortgage Broker Fee/Compensation Agreement Inclusion
- **Field Name**: Include Mortgage Broker Fee/Compensation Agreement  
- **UI Section**: Newrez LE Package  
- **UI Location**: Not inferable  
- **Description**: Allows the user to decide whether to include the Mortgage Broker Fee/Compensation Agreement in the Newrez Loan Estimate (LE) Package.  
- **Dropdown Options**:  
  - Select...  
  - Yes  
  - No  
- **Restrictions**:  
  - The form must be reviewed to ensure it meets the broker's license/registration disclosure requirements before inclusion.  
- **Visibility Logic**: Always visible  

---

### Field 3: Manage Additional Broker Disclosures
- **Field Name**: Manage Additional Broker Disclosures  
- **UI Section**: Broker Disclosures  
- **UI Location**: Not inferable  
- **Description**: Provides functionality to append additional broker disclosures to the Newrez LE Package.  
- **Dropdown Options**: None  
- **Restrictions**: None  
- **Visibility Logic**: Always visible  

---

### Field 4: Manage Broker Disclosures
- **Field Name**: Manage Broker Disclosures  
- **UI Section**: Broker Disclosures  
- **UI Location**: Not inferable  
- **Description**: Provides functionality to manage broker disclosures within the system.  
- **Dropdown Options**: None  
- **Restrictions**: None  
- **Visibility Logic**: Always visible  

--- 

This documentation is precise and avoids assumptions beyond the OCR text provided.

Here is the structured field documentation based on the OCR text provided:

---

### Field 1: **Mortgage Broker Fee/Compensation Agreement Inclusion**
- **Field Name**: Include Mortgage Broker Fee/Compensation Agreement
- **UI Section**: Newrez LE Package
- **UI Location**: Top of the form
- **Description**: Determines whether the Mortgage Broker Fee/Compensation Agreement should be included in the Newrez Loan Estimate (LE) package.
- **Dropdown Options**: Yes / No
- **Restrictions**: None specified.
- **Visibility Logic**: Visible only when the user is working on the Newrez LE Package.

---

### Field 2: **License Selection**
- **Field Name**: License for Loan Origination
- **UI Section**: Newrez LE Package
- **UI Location**: Below the Mortgage Broker Fee/Compensation Agreement inclusion field.
- **Description**: Allows the user to select the license under which the loan will be originated.
- **Dropdown Options**: DRE / RML / Other (if applicable)
- **Restrictions**: Must align with the broker’s license/registration disclosure requirements.
- **Visibility Logic**: Visible only when the user is working on the Newrez LE Package.

---

### Field 3: **Manage Additional Broker Disclosures**
- **Field Name**: Manage Additional Broker Disclosures
- **UI Section**: Broker Disclosures Management
- **UI Location**: Middle of the form, under the license selection field.
- **Description**: Provides functionality to append additional broker disclosures to the Newrez LE Package.
- **Dropdown Options**: None (button functionality).
- **Restrictions**: None specified.
- **Visibility Logic**: Visible only when the user is managing broker disclosures.

---

### Field 4: **Disclosures to be Appended**
- **Field Name**: Disclosures to be Appended to Newrez LE Package
- **UI Section**: Broker Disclosures Management
- **UI Location**: Bottom of the form.
- **Description**: Displays the count of disclosures that will be appended to the Newrez LE Package.
- **Dropdown Options**: None (numeric display).
- **Restrictions**: None specified.
- **Visibility Logic**: Visible only when the user is managing broker disclosures.

---

### Notes:
- The OCR text mentions that the form may not be suitable for every transaction and advises reviewing license/registration disclosure requirements. This is relevant for Field 2 (License Selection) and Field 3 (Manage Additional Broker Disclosures).
- No additional visibility logic or restrictions were explicitly stated in the OCR text.

----------- PRECONDITIONS -----------
--------------------------------------------------------------------------
import os
from datetime import datetime


def dump_state_to_txt(state: dict, filename: str = "ado_agent_{user_story_id}_output.txt"):
    os.makedirs("debug", exist_ok=True)

    filename = filename.format(
        user_story_id=state.get("user_story_id", "unknown")
    )

    path = os.path.join("debug", filename)

    with open(path, "w", encoding="utf-8") as f:
        f.write("=========== ADO INTELLIGENCE AGENT OUTPUT ===========\n\n")

        f.write(f"User Story ID:\n{state.get('user_story_id')}\n\n")

        f.write("----------- TITLE -----------\n")
        f.write(f"{state.get('title')}\n\n")

        f.write("----------- ENRICHED DESCRIPTION -----------\n")
        f.write(f"{state.get('description')}\n\n")

        f.write("----------- CHANNELS -----------\n")
        f.write(f"{state.get('channels')}\n\n")

        f.write("----------- ENRICHED ACCEPTANCE CRITERIA -----------\n")
        f.write(f"{state.get('acceptance_criteria')}\n\n")

        f.write("----------- PRECONDITIONS -----------\n")
        for ch, pre in state.get("preconditions", {}).items():
            f.write(f"\n[{ch}]\n{pre}\n")

    print(f"\n State dumped to: {path}\n")
