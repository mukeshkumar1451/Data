import os
import re
import requests
from bs4 import BeautifulSoup
from dotenv import load_dotenv

load_dotenv()
ADO_PAT = os.getenv("ADO_PAT")


# ---------------------------------------------------------
# DOWNLOAD ADO IMAGE
# ---------------------------------------------------------
def _download_ado_image(url: str, save_path: str) -> str:
    try:
        response = requests.get(url, auth=("", ADO_PAT))
        response.raise_for_status()

        with open(save_path, "wb") as f:
            f.write(response.content)

        return save_path
    except Exception:
        return ""


# ---------------------------------------------------------
# CLEAN TEXT
# ---------------------------------------------------------
def _normalize_text(text: str) -> str:
    if not text:
        return ""

    text = re.sub(r'\s+', ' ', text)
    return text.strip()


# ---------------------------------------------------------
# MAIN PROCESSOR (NO OCR)
# ---------------------------------------------------------
def process_html_and_download_images(html: str, story_id: str, section: str):

    if not html:
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
        downloaded_path = _download_ado_image(src, save_path)

        if downloaded_path:
            downloaded_images.append(downloaded_path)

    raw_text = soup.get_text(separator="\n")
    clean_text = _normalize_text(raw_text)

    return {
        "clean_text": clean_text,
        "image_paths": downloaded_images
    }
--------------------------------------------------------------------
import logging
import base64
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.channel_detector import detect_channels
from utils.state_debugger import dump_state_to_txt
from config.config import get

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:

    def __init__(self):
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )
        self.model = get("CHAT_MODEL")

    # ---------------------------------------------------------
    # Encode image to base64
    # ---------------------------------------------------------
    def _encode_image(self, path: str) -> str:
        with open(path, "rb") as f:
            return base64.b64encode(f.read()).decode("utf-8")

    # ---------------------------------------------------------
    # MAIN
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("🚀 ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]

        # 1️⃣ Fetch story
        story = fetch_from_ado(user_story_id)

        raw_description = story.get("description", "")
        raw_ac = story.get("acceptance_criteria", "")

        # 2️⃣ Parse HTML + Download images (NO OCR)
        description_data = process_html_and_download_images(
            raw_description,
            user_story_id,
            "description"
        )

        ac_data = process_html_and_download_images(
            raw_ac,
            user_story_id,
            "acceptance_criteria"
        )

        # 3️⃣ Build Vision Input
        vision_content = []

        vision_content.append({
            "type": "text",
            "text": f"""
User Story:
{description_data["clean_text"]}

Acceptance Criteria:
{ac_data["clean_text"]}
"""
        })

        all_images = description_data["image_paths"] + ac_data["image_paths"]

        for img_path in all_images:
            vision_content.append({
                "type": "image_url",
                "image_url": {
                    "url": f"data:image/png;base64,{self._encode_image(img_path)}"
                }
            })

        # 4️⃣ Call Azure GPT-4o
        response = self.openai.chat.completions.create(
            model=self.model,
            temperature=0,
            messages=[
                {
                    "role": "system",
                    "content": """
You are a Senior Mortgage QA Analyst.

Analyze:
1. User Story
2. Acceptance Criteria
3. UI Screenshots

Extract:
- UI field names
- Field type (checkbox, dropdown, text)
- Dropdown values
- Conditional visibility logic
- Privilege restrictions
- State-based rules

Return ONLY structured JSON.
"""
                },
                {
                    "role": "user",
                    "content": vision_content
                }
            ]
        )

        structured_output = response.choices[0].message.content

        # 5️⃣ Detect channels (optional)
        channels = detect_channels(description_data["clean_text"])

        # 6️⃣ Update state
        state["title"] = story.get("title")
        state["user_story"] = story.get("title")
        state["description"] = description_data["clean_text"]
        state["acceptance_criteria"] = ac_data["clean_text"]
        state["llm_structured_output"] = structured_output
        state["channels"] = channels

        dump_state_to_txt(state)

        logger.info("✅ ADO Intelligence Agent completed")

        return state
