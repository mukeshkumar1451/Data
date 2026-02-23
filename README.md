import logging
import json
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.channel_detector import detect_channels
from utils.state_debugger import dump_state_to_txt
from config.config import get
from utils.channel_rules import build_channel_rules

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:
    """
    Responsibilities:
    1. Fetch User Story from ADO
    2. Clean Description HTML + download images
    3. Clean Acceptance Criteria HTML + download images
    4. Detect Channels
    5. 🔥 Split story into channel-specific meaning
    6. Prepare state for retrieval agent
    """

    def __init__(self):
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )
        self.model = get("CHAT_MODEL")

    # ---------------------------------------------------------
    # Channel Context Derivation (MOST IMPORTANT PART)
    # ---------------------------------------------------------
    def _derive_channel_context(self, description: str, ac: str, channels):

        logger.info("🧠 Deriving channel specific flows from story")

        prompt = f"""
You are a mortgage workflow analyst.

Your job:
Split the story into workflow meaning for each mortgage channel.

Channel definitions:
RTL = Loan officer + borrower interaction
WHL = Broker submits package / broker compliance
DTC = Borrower self-service portal behavior
CL1 = Correspondent purchase / post-closing workflow

Rules:
• A story may contain mixed workflows
• Extract only relevant sentences per channel
• DO NOT copy entire story to every channel
• If nothing relevant → return empty string
• Be conservative (precision > recall)

DESCRIPTION:
{description}

AC:
{ac}

Return STRICT JSON:

{{
"RTL": "...",
"WHL": "...",
"DTC": "...",
"CL1": "..."
}}
"""

        try:
            resp = self.openai.chat.completions.create(
                model=self.model,
                messages=[{"role": "user", "content": prompt}],
                temperature=0
            )

            content = resp.choices[0].message.content.strip()
            parsed = json.loads(content)

            # ensure all channels exist
            for ch in channels:
                parsed.setdefault(ch, "")

            logger.info(f"✅ Channel context derived: {parsed}")
            return parsed

        except Exception as e:
            logger.warning(f"⚠️ LLM context split failed → fallback rule mode: {e}")

            
            return {ch: "" for ch in channels}

    # ---------------------------------------------------------
    # MAIN ENTRY
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:
        logger.info("🚀 ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]

        # 1️⃣ Fetch
        story = fetch_from_ado(user_story_id)

        # 2️⃣ Clean HTML
        logger.info("🧹 Processing Description HTML + Images...")
        description_enriched = process_html_and_download_images(
            story["description"], user_story_id, "description"
        )

        logger.info("🧹 Processing Acceptance Criteria HTML + Images...")
        ac_enriched = process_html_and_download_images(
            story["acceptance_criteria"], user_story_id, "ac"
        )

        # 3️⃣ Detect channels
        channels = detect_channels(ac_enriched)
        channel_rules_map ={
            ch: build_channel_rules(ch) for ch in channels
        }
        logger.info(f"✅ Channels detected: {channels}")

        # 🔥 4️⃣ NEW — derive channel context
        channel_context_map = self._derive_channel_context(
            description_enriched,
            ac_enriched,
            channels
        )

        # Debug output
        print("\n=========== ADO INTELLIGENCE AGENT OUTPUT ===========\n")
        print(f"User Story ID: {user_story_id}")
        print("TITLE:", story["title"])
       # logger.info(f"CHANNEL CONTEXT MAP:\n{json.dumps(channel_context_map, indent=2)}")
        print("\n=====================================================\n")

        dump_state_to_txt({
            "user_story_id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
            "channels": channels,
            "channel_context_map": channel_context_map
        })

        # -------------------------------------------------
        # STATE MUTATION
        # -------------------------------------------------
        state["user_story"] = story["title"]
        state["description"] = description_enriched
        state["acceptance_criteria"] = ac_enriched
        state["channels"] = channels
        state["channel_rules_map"] = channel_rules_map

        # 🔥 NEW DATA FOR RETRIEVAL AGENT
        state["channel_context_map"] = channel_context_map

        state["story"] = {
            "id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
        }

        return state
----------------------------------------------
# utils/html_image_processor.py

import os
import re
import requests
from bs4 import BeautifulSoup
from dotenv import load_dotenv

load_dotenv()
ADO_PAT = os.getenv("ADO_PAT")


# ---------------------------------------------------------
# Download ADO image
# ---------------------------------------------------------
def _download_ado_image(url: str, save_path: str) -> str:
    try:
        response = requests.get(url, auth=("", ADO_PAT))
        response.raise_for_status()

        with open(save_path, "wb") as f:
            f.write(response.content)

        return save_path
    except Exception as e:
        return f"[Image download failed: {e}]"


# ---------------------------------------------------------
# Clean extracted text (VERY IMPORTANT FOR LLM)
# ---------------------------------------------------------
def _normalize_text(text: str) -> str:
    if not text:
        return ""

    #  Fix broken words: sh ould -> should
    text = re.sub(r'(\w)\s{2,}(\w)', r'\1\2', text)

    #  Remove excessive spaces
    text = re.sub(r'[ \t]+', ' ', text)

    #  Join broken lines but keep paragraphs
    text = re.sub(r'\n(?=[a-z])', ' ', text)
    text = re.sub(r'\n{2,}', '\n', text)

    #  Remove dot noise produced by ADO formatting
    text = re.sub(r'\.\s*\.\s*\.\s*', ' ', text)

    #  Remove space before punctuation
    text = re.sub(r'\s+([.,!?])', r'\1', text)

    #  Normalize colon spacing (important for extractors)
    text = re.sub(r'\s*:\s*', ': ', text)

    return text.strip()


# ---------------------------------------------------------
# MAIN FUNCTION
# ---------------------------------------------------------
def process_html_and_download_images(html: str, story_id: str, section: str) -> str:
    """
    Extract readable text + download images from ADO HTML

    section = 'description' or 'ac'
    """

    if not html:
        return ""

    soup = BeautifulSoup(html, "html.parser")

    # remove unwanted tags
    for tag in soup(["script", "style"]):
        tag.decompose()

    # -------------------------------------------------
    # IMAGE DOWNLOAD
    # -------------------------------------------------
    images = soup.find_all("img")

    img_folder = os.path.join("downloads", story_id, section)
    os.makedirs(img_folder, exist_ok=True)

    image_references = []

    for idx, img in enumerate(images, start=1):
        src = img.get("src")
        if not src:
            continue

        save_path = os.path.join(img_folder, f"image_{idx}.png")
        downloaded_path = _download_ado_image(src, save_path)

        #image_references.append(f"[IMAGE DOWNLOADED: {downloaded_path}]")

    # -------------------------------------------------
    # TEXT EXTRACTION
    # -------------------------------------------------
    raw_text = soup.get_text(separator="\n")

    clean_text = _normalize_text(raw_text)

    # -------------------------------------------------
    # APPEND IMAGE REFERENCES
    # -------------------------------------------------
    if image_references:
        final_text = clean_text + "\n\n" + "\n".join(image_references)
    else:
        final_text = clean_text

    return final_text
------------------------------------------------------------
downloads\718521\ac\image_1.png
downloads\718521\description\image_1.png
