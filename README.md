import os
import re
import requests
from bs4 import BeautifulSoup
from dotenv import load_dotenv

from utils.image_ocr_processor import extract_text_from_image, clean_ocr_text

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
    except Exception as e:
        return ""


# ---------------------------------------------------------
# CLEAN TEXT
# ---------------------------------------------------------
def _normalize_text(text: str) -> str:
    if not text:
        return ""

    text = re.sub(r'(\w)\s{2,}(\w)', r'\1\2', text)
    text = re.sub(r'[ \t]+', ' ', text)
    text = re.sub(r'\n(?=[a-z])', ' ', text)
    text = re.sub(r'\n{2,}', '\n', text)
    text = re.sub(r'\.\s*\.\s*\.\s*', ' ', text)
    text = re.sub(r'\s+([.,!?])', r'\1', text)
    text = re.sub(r'\s*:\s*', ': ', text)

    return text.strip()


# ---------------------------------------------------------
# MAIN PROCESSOR
# ---------------------------------------------------------
def process_html_and_download_images(html: str, story_id: str, section: str):

    if not html:
        return ""

    soup = BeautifulSoup(html, "html.parser")

    for tag in soup(["script", "style"]):
        tag.decompose()

    images = soup.find_all("img")

    img_folder = os.path.join("downloads", story_id, section)
    os.makedirs(img_folder, exist_ok=True)

    all_ocr_text = []

    for idx, img in enumerate(images, start=1):
        src = img.get("src")
        if not src:
            continue

        save_path = os.path.join(img_folder, f"image_{idx}.png")
        downloaded_path = _download_ado_image(src, save_path)

        if downloaded_path:
            ocr_text = extract_text_from_image(downloaded_path)

            if ocr_text and len(ocr_text.strip()) > 10:
                cleaned_ocr = clean_ocr_text(ocr_text)

                if cleaned_ocr:
                    all_ocr_text.append(str(cleaned_ocr))

    raw_text = soup.get_text(separator="\n")
    clean_text = _normalize_text(raw_text)

    if all_ocr_text:
        combined_ocr = "\n".join(
            [str(x) for x in all_ocr_text if x]
        )
        final_text = clean_text + "\n\n" + combined_ocr
    else:
        final_text = clean_text

    return final_text
--------------------------------------------------------------
import pytesseract
import cv2
import os
import re
from openai import AzureOpenAI
from config.config import get


# ---------------------------------------------------------
# OCR EXTRACTION
# ---------------------------------------------------------
def extract_text_from_image(image_path: str) -> str:
    try:
        if not os.path.exists(image_path):
            print(f"OCR DEBUG: File not found -> {image_path}")
            return ""

        image = cv2.imread(image_path)

        if image is None:
            print(f"OCR DEBUG: cv2 could not read image -> {image_path}")
            return ""

        gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
        gray = cv2.threshold(gray, 150, 255, cv2.THRESH_BINARY)[1]

        text = pytesseract.image_to_string(gray)

        print(f"OCR DEBUG: Extracted text length = {len(text)}")

        return text.strip()

    except Exception as e:
        print(f"OCR ERROR: {e}")
        return ""

   


# ---------------------------------------------------------
# STRUCTURE OCR TEXT INTO UI FIELD FORMAT
# ---------------------------------------------------------
def structure_ocr_text(ocr_text: str) -> str:
    if not ocr_text:
        return ""

    client = AzureOpenAI(
        api_key=get("AZURE_OPENAI_KEY"),
        azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
        api_version=get("AZURE_OPENAI_API_VERSION"),
    )

    model = get("CHAT_MODEL")

    prompt = f"""
You are a mortgage UI analyst.

Convert this OCR extracted UI text into structured field documentation.

For each field identify:
- Field Name
- UI Section
- UI Location (if inferable)
- Description
- Dropdown Options (if any)
- Restrictions (if any)
- Visibility Logic (if any)

Be precise.
Do not hallucinate.

OCR TEXT:
{ocr_text}
"""

    try:
        resp = client.chat.completions.create(
            model=model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        return resp.choices[0].message.content.strip()

    except Exception as e:
        return f"STRUCTURE_FAILED: {e}"

def clean_ocr_text(text: str) -> str:
    if not text:
        return ""

    # Fix common OCR mistakes
    replacements = {
        "Ceypass": "Bypass",
        "Oi": "Of",
        "Olnpm": "HPML",
        "D allow": "Allow",
        "4 Electronic Deli": "Electronic Delivery",
        "¥": "",
        "|": "",
        "v]": "",
    }

    for wrong, correct in replacements.items():
        text = text.replace(wrong, correct)

    # Remove excessive spaces
    text = re.sub(r"[ \t]+", " ", text)

    # Remove duplicate blank lines
    text = re.sub(r"\n{2,}", "\n", text)    
---- ----------------------------------------------------
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
    3. Extract OCR text from images
    4. Structure OCR UI fields
    5. Detect Channels
    6. Derive channel-specific workflow context
    7. Generate structured business summary
    8. Prepare state for retrieval agent
    """

    # ---------------------------------------------------------
    # INIT
    # ---------------------------------------------------------
    def __init__(self):
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )
        self.model = get("CHAT_MODEL")

    # ---------------------------------------------------------
    # CHANNEL CONTEXT DERIVATION
    # ---------------------------------------------------------
    def _derive_channel_context(self, description: str, ac: str, channels):

        logger.info("🧠 Deriving channel specific flows")

        prompt = f"""
You are a mortgage workflow analyst.

Split the story into channel-specific meaning.

Channel definitions:
RTL = Loan officer + borrower interaction
WHL = Broker submits package / broker compliance
DTC = Borrower self-service portal behavior
CL1 = Correspondent purchase / post-closing workflow

Rules:
• Extract only relevant sentences per channel
• Do NOT copy entire story
• If nothing relevant → return empty string
• Precision over recall

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

            for ch in channels:
                parsed.setdefault(ch, "")

            return parsed

        except Exception as e:
            logger.warning(f"Channel derivation failed: {e}")
            return {ch: "" for ch in channels}

    # ---------------------------------------------------------
    # FINAL STRUCTURED SUMMARY
    # ---------------------------------------------------------
    def _generate_structured_story_summary(
        self,
        story_id: str,
        title: str,
        description: str,
        ac: str,
        channels: list
    ) -> str:

        logger.info("📄 Generating structured business summary")

        prompt = f"""
Generate structured documentation EXACTLY in this format:

User Story {story_id}
Title: {title}

🔹 Business Requirement
(2–3 paragraph explanation)

🔹 UI Field Details and Locations
For each field:
- Field Name
- UI Location
- Description
- Dropdown Options (if any)
- Associated Controls (if any)
- Restrictions (if any)
- Visibility Logic (if any)

🔹 Channels Impacted
List channels exactly as provided.

🔹 Acceptance Criteria
Structured bullets including:
- Fields required in audit
- Audit capture expectations
- Visibility rules
- Channel consistency requirements

DESCRIPTION:
{description}

AC:
{ac}

CHANNELS:
{channels}

Do not hallucinate.
Use only provided data.
"""

        try:
            resp = self.openai.chat.completions.create(
                model=self.model,
                messages=[{"role": "user", "content": prompt}],
                temperature=0
            )

            return resp.choices[0].message.content.strip()

        except Exception as e:
            logger.error(f"Structured summary generation failed: {e}")
            return ""

    # ---------------------------------------------------------
    # MAIN ENTRY
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("🚀 ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]

        # 1️⃣ Fetch from ADO
        story = fetch_from_ado(user_story_id)

        # 2️⃣ Process Description (HTML + Images + OCR)
        logger.info("🧹 Processing Description...")
        description_enriched = process_html_and_download_images(
            story["description"],
            user_story_id,
            "description"
        )

        # 3️⃣ Process Acceptance Criteria
        logger.info("🧹 Processing Acceptance Criteria...")
        ac_enriched = process_html_and_download_images(
            story["acceptance_criteria"],
            user_story_id,
            "ac"
        )

        # 4️⃣ Detect Channels
        channels = detect_channels(ac_enriched)
        logger.info(f"✅ Channels detected: {channels}")

        channel_rules_map = {
            ch: build_channel_rules(ch) for ch in channels
        }

        # 5️⃣ Derive Channel Context
        channel_context_map = self._derive_channel_context(
            description_enriched,
            ac_enriched,
            channels
        )

        # 6️⃣ Generate Final Structured Summary
        structured_summary = self._generate_structured_story_summary(
            user_story_id,
            story["title"],
            description_enriched,
            ac_enriched,
            channels
        )

        # -------------------------------------------------
        # DEBUG OUTPUT
        # -------------------------------------------------
        print("\n=========== ADO INTELLIGENCE AGENT OUTPUT ===========\n")
        print(f"User Story ID: {user_story_id}")
        print("TITLE:", story["title"])
        print("\n=====================================================\n")

        dump_state_to_txt({
            "user_story_id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
            "channels": channels,
            "channel_context_map": channel_context_map,
            "structured_summary": structured_summary
        })

        # -------------------------------------------------
        # STATE MUTATION
        # -------------------------------------------------
        state["user_story"] = story["title"]
        state["description"] = description_enriched
        state["acceptance_criteria"] = ac_enriched
        state["channels"] = channels
        state["channel_rules_map"] = channel_rules_map
        state["channel_context_map"] = channel_context_map
        state["structured_summary"] = structured_summary

        state["story"] = {
            "id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
        }

        logger.info("✅ ADO Intelligence Agent completed successfully")

        return state
