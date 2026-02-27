import json
import logging

logger = logging.getLogger(__name__)


def convert_json_to_grouped_steps(llm_output: str) -> str:
    try:
        data = json.loads(llm_output)
        logger.info("✅ LLM JSON successfully parsed")
    except Exception as e:
        logger.error(f"❌ JSON parsing failed: {e}")
        return llm_output

    output_blocks = []
    fields = data.get("fields", [])

    logger.info(f"🔎 Total fields extracted: {len(fields)}")

    for field in fields:
        name = field.get("name", "")
        field_type = field.get("type", "")
        values = field.get("values", [])
        location = field.get("location", "")
        visibility = field.get("visibilityRule", "")
        privilege = field.get("privilegeRestricted", False)

        block_lines = []

        # 🔹 Field Title Line (HPML, Intent to Proceed, etc.)
        block_lines.append(name)

        if location:
            block_lines.append(f"Navigate to {location}.")

        block_lines.append(
            f'Verify that the "{name}" field is rendered as a {field_type}.'
        )

        if values:
            block_lines.append(
                'Verify that the dropdown contains exactly the following options:'
            )
            for val in values:
                block_lines.append(f"    - {val}")

        if visibility:
            block_lines.append(
                f'Verify that the field is visible when: {visibility}.'
            )

        if privilege:
            block_lines.append(
                "Verify that the field is restricted based on user privilege."
            )
        else:
            block_lines.append(
                "Verify that the field is not privilege restricted."
            )

        output_blocks.append("\n".join(block_lines))

    return "\n\n".join(output_blocks)
-----------------------------------------------------------------
import os
import logging
from datetime import datetime

logger = logging.getLogger(__name__)


def save_final_txt(story_id, title, description, grouped_steps):

    folder = os.path.join("llm_outputs", story_id)
    os.makedirs(folder, exist_ok=True)

    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    file_path = os.path.join(folder, f"{story_id}_{timestamp}.txt")

    with open(file_path, "w", encoding="utf-8") as f:
        f.write("=====================================\n")
        f.write("ADO INTELLIGENCE ANALYSIS OUTPUT\n")
        f.write("=====================================\n\n\n")

        f.write(f"Story ID: {story_id}\n")
        f.write(f"Title: {title}\n")
        f.write(f"Timestamp: {timestamp}\n")

        f.write("------------ DESCRIPTION ------------\n")
        f.write(description + "\n")

        f.write("------ ACCEPTANCE CRITERIA ----------\n")
        f.write(grouped_steps)

    logger.info(f"✅ Final formatted TXT saved: {file_path}")
    return file_path
