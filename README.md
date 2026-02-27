import json
import logging
import re

logger = logging.getLogger(__name__)


def extract_json_from_response(text: str):
    """
    Safely extract JSON object from GPT response.
    Handles:
    - Markdown ```json blocks
    - Extra explanation text
    - Leading/trailing whitespace
    """
    try:
        if not text or not text.strip():
            logger.error("❌ Empty LLM response")
            return None

        # Remove markdown wrappers
        text = re.sub(r"```json", "", text)
        text = re.sub(r"```", "", text)

        # Extract first JSON object
        match = re.search(r"\{.*\}", text, re.DOTALL)

        if not match:
            logger.error("❌ No JSON object found in response")
            return None

        return json.loads(match.group())

    except Exception as e:
        logger.error(f"❌ JSON extraction failed: {e}")
        return None


def convert_json_to_grouped_steps(llm_output: str) -> str:
    data = extract_json_from_response(llm_output)

    if not data:
        logger.error("❌ Unable to parse LLM JSON. Returning raw output.")
        return llm_output  # fallback

    fields = data.get("fields", [])

    if not fields:
        logger.warning("⚠ No fields found in LLM output")

    output_blocks = []

    for field in fields:
        name = field.get("name", "")
        field_type = field.get("type", "")
        values = field.get("values", [])
        location = field.get("location", "")
        visibility = field.get("visibilityRule", "")
        privilege = field.get("privilegeRestricted", False)

        block_lines = []
        block_lines.append(name)

        if location:
            block_lines.append(f"Navigate to {location}.")

        block_lines.append(
            f'Verify that the "{name}" field is rendered as a {field_type}.'
        )

        if values:
            block_lines.append(
                "Verify that the dropdown contains exactly the following options:"
            )
            for val in values:
                block_lines.append(f"    - {val}")

        if visibility:
            block_lines.append(
                f"Verify that the field is visible when: {visibility}."
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
------------------------------------------------------------------------------------------------
import os
import logging
from datetime import datetime

logger = logging.getLogger(__name__)


def save_final_txt(story_id, title, description, grouped_steps):
    """
    Expected 4 parameters only.
    """

    folder = os.path.join("llm_outputs", story_id)
    os.makedirs(folder, exist_ok=True)

    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    file_path = os.path.join(folder, f"{story_id}_{timestamp}.txt")

    try:
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

        logger.info(f"✅ TXT file saved successfully: {file_path}")

    except Exception as e:
        logger.error(f"❌ Failed to write TXT file: {e}")

    return file_path
----------------------------------------------------------------
import logging
import base64
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.step_generator import convert_json_to_grouped_steps
from utils.output_writer import save_final_txt
from config.config import get

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:

    def __init__(self):
        self.client = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )
        self.model = get("CHAT_MODEL")

    def _encode_image(self, path):
        with open(path, "rb") as f:
            return base64.b64encode(f.read()).decode("utf-8")

    def run(self, state: dict):

        logger.info("🚀 ADO Intelligence Agent started")

        story_id = state["user_story_id"]

        # 1️⃣ Fetch ADO Story
        story = fetch_from_ado(story_id)
        logger.info("✅ ADO story fetched")

        # 2️⃣ Process HTML + Images
        desc_data = process_html_and_download_images(
            story["description"], story_id, "description"
        )

        ac_data = process_html_and_download_images(
            story["acceptance_criteria"], story_id, "acceptance_criteria"
        )

        # 3️⃣ Prepare Vision Input
        vision_content = [{
            "type": "text",
            "text": f"""
User Story:
{desc_data['clean_text']}

Acceptance Criteria:
{ac_data['clean_text']}
"""
        }]

        all_images = desc_data["image_paths"] + ac_data["image_paths"]

        logger.info(f"📷 Sending {len(all_images)} images to GPT-4o")

        for img in all_images:
            vision_content.append({
                "type": "image_url",
                "image_url": {
                    "url": f"data:image/png;base64,{self._encode_image(img)}"
                }
            })

        # 4️⃣ Call GPT-4o (Force JSON Response)
        response = self.client.chat.completions.create(
            model=self.model,
            temperature=0,
            response_format={"type": "json_object"},
            messages=[
                {
                    "role": "system",
                    "content": """
You are a Senior Mortgage QA Analyst.

Extract structured JSON:
{
  "fields": [
    {
      "name": "",
      "type": "",
      "values": [],
      "location": "",
      "visibilityRule": "",
      "privilegeRestricted": false
    }
  ]
}

Return JSON only.
"""
                },
                {
                    "role": "user",
                    "content": vision_content
                }
            ]
        )

        logger.info("✅ GPT-4o response received")

        llm_output = response.choices[0].message.content

        # 5️⃣ Convert to Grouped QA Steps
        grouped_steps = convert_json_to_grouped_steps(llm_output)

        # 6️⃣ Save TXT (Correct 4 arguments only)
        save_final_txt(
            story_id,
            story["title"],
            desc_data["clean_text"],
            grouped_steps
        )

        logger.info("🎯 ADO Intelligence Agent completed successfully")

        return state
