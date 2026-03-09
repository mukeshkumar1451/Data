prompt = f"""
You are a QA automation analyst analyzing a UI screenshot from a mortgage application system.

User Story Context:
{description}

Your task is to carefully analyze the screenshot and extract all UI elements.

Focus especially on these fields if present:
- HPML (Higher Priced Mortgage Loan)
- Intent to Proceed
- Mortgage Broker Fee/Compensation Agreement
- Mortgage Broker License Type

Instructions:
1. Identify the SECTION name in the UI.
2. Identify all FIELDS inside that section.
3. Extract the exact UI label text.
4. Identify the FIELD TYPE (Dropdown, Checkbox, Textbox).
5. If it is a dropdown, list ALL visible options.
6. If it is a checkbox, identify whether it is Checked or Unchecked.
7. Identify any BUTTONS in the section.
8. Do not hallucinate fields not visible in the screenshot.

Important Mapping Rules:
If UI labels are long sentences, map them to the closest field name.

Examples:
"Do you want to include Mortgage Broker Fee/Compensation Agreement in the Newrez LE Package?"
→ Mortgage Broker Fee/Compensation Agreement

"Under which license will you originate this loan?"
→ Mortgage Broker License Type

Return the output strictly in the following format:

=====================================
IMAGE ANALYSIS OUTPUT
=====================================

Section: <Section Name>

Fields:

1. <Field Name>
   UI Label:
   <Label text>

   Field Type:
   <Dropdown / Checkbox / Textbox>

   Values:
   - <Option1>
   - <Option2>

Buttons:
- <Button Name>

=====================================
END OF IMAGE ANALYSIS
=====================================
"""
