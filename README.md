import logging
import os


def setup_logger():
    log_folder = "logs"
    os.makedirs(log_folder, exist_ok=True)

    log_file = os.path.join(log_folder, "ado_intelligence.log")

    logging.basicConfig(
        level=logging.INFO,
        format="%(asctime)s | %(levelname)s | %(name)s | %(message)s",
        handlers=[
            logging.FileHandler(log_file, encoding="utf-8"),
            logging.StreamHandler()
        ]
    )
----------------------------------------------------------------------
import os
import re
import requests
import logging
from bs4 import BeautifulSoup
from dotenv import load_dotenv

logger = logging.getLogger(__name__)

load_dotenv()
ADO_PAT = os.getenv("ADO_PAT")


def _download_ado_image(url: str, save_path: str) -> str:
    try:
        response = requests.get(url, auth=("", ADO_PAT))
        response.raise_for_status()

        with open(save_path, "wb") as f:
            f.write(response.content)

        logger.info(f"✅ Image downloaded: {save_path}")
        return save_path

    except Exception as e:
        logger.error(f"❌ Image download failed: {e}")
        return ""


def _normalize_text(text: str) -> str:
    if not text:
        return ""
    return re.sub(r"\s+", " ", text).strip()


def process_html_and_download_images(html: str, story_id: str, section: str):

    logger.info(f"🔎 Processing HTML for section: {section}")

    if not html:
        logger.warning("⚠ Empty HTML content")
        return {"clean_text": "", "image_paths": []}

    soup = BeautifulSoup(html, "html.parser")

    for tag in soup(["script", "style"]):
        tag.decompose()

    images = soup.find_all("img")

    img_folder = os.path.join("downloads", story_id, section)
    os.makedirs(img_folder, exist_ok=True)

    downloaded_images = []

    for idx, img in enumerate(images, start=1):
        src = img.get("src")
        if not src:
            continue

        save_path = os.path.join(img_folder, f"image_{idx}.png")
        downloaded = _download_ado_image(src, save_path)

        if downloaded:
            downloaded_images.append(downloaded)

    clean_text = _normalize_text(soup.get_text(separator="\n"))

    logger.info(f"✅ HTML processed. Images found: {len(downloaded_images)}")

    return {
        "clean_text": clean_text,
        "image_paths": downloaded_images
    }
----------------------------------------------------------------------------------------------------
import json
import logging

logger = logging.getLogger(__name__)


def convert_json_to_step_sentences(llm_output: str) -> str:
    try:
        data = json.loads(llm_output)
        logger.info("✅ LLM JSON successfully parsed")
    except Exception as e:
        logger.error(f"❌ JSON parsing failed: {e}")
        return llm_output

    step_blocks = []
    fields = data.get("fields", [])

    logger.info(f"🔎 Fields extracted from LLM: {len(fields)}")

    for field in fields:
        steps = []

        name = field.get("name")
        field_type = field.get("type")
        values = field.get("values", [])
        location = field.get("location")
        visibility = field.get("visibilityRule")
        privilege = field.get("privilegeRestricted")

        if location:
            steps.append(f"Navigate to {location}.")

        steps.append(
            f'Verify that the "{name}" field is rendered as a {field_type}.'
        )

        if values:
            steps.append(
                f'Verify that the dropdown contains exactly the following options:'
            )
            for val in values:
                steps.append(f"    - {val}")

        if visibility:
            steps.append(
                f'Verify that the field is visible when: {visibility}.'
            )

        if privilege:
            steps.append(
                "Verify that the field is restricted based on user privilege."
            )
        else:
            steps.append(
                "Verify that the field is not privilege restricted."
            )

        step_blocks.append("\n".join(steps))

    return "\n\n----------------------------------\n\n".join(step_blocks)
-------------------------------------------------------------------------
import os
import logging
from datetime import datetime

logger = logging.getLogger(__name__)


def save_final_txt(story_id, title, description, ac, steps_text):

    folder = os.path.join("llm_outputs", story_id)
    os.makedirs(folder, exist_ok=True)

    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    file_path = os.path.join(folder, f"{story_id}_{timestamp}.txt")

    with open(file_path, "w", encoding="utf-8") as f:
        f.write("=====================================\n")
        f.write("ADO INTELLIGENCE ANALYSIS OUTPUT\n")
        f.write("=====================================\n\n")

        f.write(f"Story ID: {story_id}\n")
        f.write(f"Title: {title}\n")
        f.write(f"Timestamp: {timestamp}\n\n")

        f.write("------------ DESCRIPTION ------------\n")
        f.write(description + "\n\n")

        f.write("------ ACCEPTANCE CRITERIA ----------\n")
        f.write(ac + "\n\n")

        f.write("---------- UI VALIDATION STEPS ----------\n\n")
        f.write(steps_text + "\n")

    logger.info(f"✅ Final TXT saved: {file_path}")
    return file_path
---------------------------------------------------------------------------------
import logging
import base64
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.step_generator import convert_json_to_step_sentences
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

        # 1️⃣ Fetch
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

        # 4️⃣ Call GPT-4o
        response = self.client.chat.completions.create(
            model=self.model,
            temperature=0,
            messages=[
                {
                    "role": "system",
                    "content": """
You are a Senior Mortgage QA Analyst.

Extract structured JSON with:
- name
- type
- values
- location
- visibilityRule
- privilegeRestricted

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

        # 5️⃣ Convert JSON to QA Steps
        steps_text = convert_json_to_step_sentences(llm_output)

        # 6️⃣ Save TXT
        save_final_txt(
            story_id,
            story["title"],
            desc_data["clean_text"],
            ac_data["clean_text"],
            steps_text
        )

        logger.info("🎯 ADO Intelligence Agent completed successfully")

        return state
------------------------------------------------------------------------- 
    
    
