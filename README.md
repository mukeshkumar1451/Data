import json
import logging
import re

logger = logging.getLogger(__name__)


# ---------------------------------------------------------
# Safe JSON Extraction
# ---------------------------------------------------------
def extract_json_from_response(text: str):
    try:
        if not text or not text.strip():
            logger.error("❌ Empty LLM response")
            return None

        text = re.sub(r"```json", "", text)
        text = re.sub(r"```", "", text)

        match = re.search(r"\{.*\}", text, re.DOTALL)

        if not match:
            logger.error("❌ No JSON object found in response")
            return None

        return json.loads(match.group())

    except Exception as e:
        logger.error(f"❌ JSON extraction failed: {e}")
        return None


# ---------------------------------------------------------
# Generate Intelligent QA Steps
# ---------------------------------------------------------
def convert_json_to_grouped_steps(llm_output: str) -> str:

    data = extract_json_from_response(llm_output)

    if not data:
        logger.error("❌ Unable to parse LLM JSON. Returning raw output.")
        return llm_output

    fields = data.get("fields", [])

    output_blocks = []

    for field in fields:
        name = field.get("name", "")
        field_type = field.get("type", "").lower()
        values = field.get("values", [])
        location = field.get("location", "")
        visibility = field.get("visibilityRule", "")
        privilege = field.get("privilegeRestricted", False)

        block = []
        block.append(name)

        # ---------------- Navigation ----------------
        if location:
            block.append(f"Navigate to {location}.")

        # ---------------- Field Type ----------------
        block.append(
            f'Verify that the "{name}" field is rendered as a {field_type.capitalize()}.'
        )

        # ---------------- Dropdown Validation ----------------
        if field_type == "dropdown" and values:

            block.append(
                f'Verify that the "{name}" dropdown contains exactly the following options:'
            )

            for val in values:
                block.append(f"    - {val}")

            # -------- YES/NO Conditional Logic --------
            if "Yes" in values and "No" in values:

                block.append(f'Select "Yes" from the "{name}" dropdown.')

                if visibility:
                    block.append(
                        f"Verify that dependent fields are visible when: {visibility}."
                    )
                else:
                    block.append(
                        "Verify that related dependent fields or sections are displayed."
                    )

                block.append(f'Select "No" from the "{name}" dropdown.')
                block.append(
                    "Verify that dependent fields or sections are hidden."
                )

        # ---------------- Checkbox Validation ----------------
        elif field_type == "checkbox":

            block.append(
                f'Verify that the "{name}" checkbox can be checked and unchecked.'
            )

            block.append(f'Check the "{name}" checkbox.')

            if visibility:
                block.append(
                    f"Verify that dependent fields are visible when: {visibility}."
                )

            block.append(f'Uncheck the "{name}" checkbox.')
            block.append(
                "Verify that dependent fields are hidden when unchecked."
            )

        # ---------------- Visibility Rule ----------------
        if visibility and field_type != "dropdown":
            block.append(
                f"Verify that the field is visible when: {visibility}."
            )

        # ---------------- Privilege Validation ----------------
        if privilege:
            block.append(
                "Verify that the field is restricted based on user privilege."
            )
        else:
            block.append(
                "Verify that the field is not privilege restricted."
            )

        output_blocks.append("\n".join(block))

    return "\n\n".join(output_blocks)
