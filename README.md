import logging
import base64
from bs4 import BeautifulSoup
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.step_generator import convert_json_to_grouped_steps
from utils.channel_detector import detect_channels
from utils.output_writer import save_final_txt
from utils.image_optimizer import resize_image_if_needed
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

    # ---------------------------------------------------------
    # Encode image to Base64
    # ---------------------------------------------------------
    def _encode_image(self, path: str) -> str:
        try:
            with open(path, "rb") as f:
                return base64.b64encode(f.read()).decode("utf-8")
        except Exception as e:
            logger.error(f"Failed to encode image {path}: {e}")
            return ""

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("ADO Intelligence Agent started")

        story_id = state["user_story_id"]

        # -----------------------------------------------------
        # 1️⃣ Fetch ADO Work Item
        # -----------------------------------------------------
        story = fetch_from_ado(story_id)
        logger.info("ADO story fetched successfully")

        raw_description = story.get("description", "")
        raw_ac = story.get("acceptance_criteria", "")

        # -----------------------------------------------------
        # 2️⃣ Clean DESCRIPTION (TEXT ONLY)
        # -----------------------------------------------------
        soup_desc = BeautifulSoup(raw_description, "html.parser")
        for tag in soup_desc(["script", "style"]):
            tag.decompose()

        clean_description = soup_desc.get_text(separator="\n").strip()
        logger.info("Description cleaned (no images downloaded)")

        # -----------------------------------------------------
        # 3️⃣ Process ACCEPTANCE CRITERIA (Download Images)
        # -----------------------------------------------------
        ac_data = process_html_and_download_images(
            raw_ac, story_id, "acceptance_criteria"
        )

        clean_ac = ac_data["clean_text"]
        ac_images = ac_data["image_paths"]

        if not ac_images:
            logger.warning("No Acceptance Criteria images found.")
            logger.info("Proceeding with text-only GPT extraction.")
        else:
            logger.info(f"AC Images downloaded: {len(ac_images)}")

        # -----------------------------------------------------
        # 4️⃣ Build Vision Input (AC Images Only)
        # -----------------------------------------------------
        vision_content = [{
            "type": "text",
            "text": f"""
User Story:
{clean_description}

Acceptance Criteria:
{clean_ac}
"""
        }]

        for img_path in ac_images:

            #  Resize before sending
            optimized_path = resize_image_if_needed(img_path)

            encoded_image = self._encode_image(optimized_path)

            if encoded_image:
                vision_content.append({
                    "type": "image_url",
                    "image_url": {
                        "url": f"data:image/jpeg;base64,{encoded_image}"
                    }
                })

        logger.info("Sending request to Azure GPT-4o")

        # -----------------------------------------------------
        # 5️⃣ Call Azure GPT-4o (Force JSON)
        # -----------------------------------------------------
        response = self.client.chat.completions.create(
            model=self.model,
            temperature=0,
            response_format={"type": "json_object"},
            messages=[
                {
                    "role": "system",
                    "content": """
You are a Senior Mortgage QA Analyst.

Extract structured JSON strictly in this format:

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

        logger.info(" GPT-4o response received")

        llm_output = response.choices[0].message.content

        # -----------------------------------------------------
        # 6️⃣ Convert JSON → Grouped Acceptance Criteria
        # -----------------------------------------------------
        grouped_steps = convert_json_to_grouped_steps(llm_output)
        logger.info(" Structured Acceptance Criteria generated")

        # -----------------------------------------------------
        # 7️⃣ Detect Channels from RAW AC
        # -----------------------------------------------------
        channels = detect_channels(clean_ac)
        logger.info(f" Channels detected from RAW AC: {channels}")

        # -----------------------------------------------------
        # 8️⃣ Save Output File
        # -----------------------------------------------------
        save_final_txt(
            story_id,
            story.get("title"),
            clean_description,
            grouped_steps
        )

        # -----------------------------------------------------
        # 9️⃣ Update State (For Retrieval Agent)
        # -----------------------------------------------------
        state["story_id"] = story_id
        state["title"] = story.get("title")
        state["user_story"] = story.get("title")
        state["description"] = clean_description

        # Raw AC (kept for reference)
        state["original_acceptance_criteria"] = clean_ac

        # Final Acceptance Criteria (LLM Generated)
        state["acceptance_criteria"] = grouped_steps

        state["llm_output"] = llm_output
        state["channels"] = channels

        logger.info(f"Final state keys: {list(state.keys())}")
        logger.info("ADO Intelligence Agent completed successfully")

        return state
-------------------------------------------------------------------------
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

       # logger.info(f" Image downloaded: {save_path}")
        return save_path

    except Exception as e:
        logger.error(f" Image download failed: {e}")
        return ""


def _normalize_text(text: str) -> str:
    if not text:
        return ""
    return re.sub(r"\s+", " ", text).strip()


def process_html_and_download_images(html: str, story_id: str, section: str):

    logger.info(f" Processing HTML for section: {section}")

    if not html:
        logger.warning(" Empty HTML content")
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

    logger.info(f" HTML processed. Images found: {len(downloaded_images)}")

    return {
        "clean_text": clean_text,
        "image_paths": downloaded_images
    }
-------------------------------------------------------------------------------------------------
import logging
from PIL import Image

logger = logging.getLogger(__name__)

MAX_WIDTH = 1200
JPEG_QUALITY = 85


def resize_image_if_needed(image_path: str) -> str:
    try:
        with Image.open(image_path) as img:
            width, height = img.size

            if width <= MAX_WIDTH:
                return image_path

            ratio = MAX_WIDTH / float(width)
            new_height = int(height * ratio)

            resized_img = img.resize((MAX_WIDTH, new_height), Image.LANCZOS)

            resized_path = image_path.replace(".png", "_resized.jpg")

            resized_img.convert("RGB").save(
                resized_path,
                "JPEG",
                quality=JPEG_QUALITY,
                optimize=True
            )

          #  logger.info(f" Resized: {image_path} → {resized_path}")

            return resized_path

    except Exception as e:
        logger.error(f" Image resize failed: {e}")
        return image_path
	
	
