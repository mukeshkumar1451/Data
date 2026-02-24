import logging
import json
import re
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.channel_detector import detect_channels
from utils.state_debugger import dump_state_to_txt
from config.config import get
from utils.channel_rules import build_channel_rules

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
    # SAFE JSON EXTRACTOR
    # ---------------------------------------------------------
    def _safe_json_extract(self, content: str):
        match = re.search(r"\{.*\}", content, re.DOTALL)
        if match:
            try:
                return json.loads(match.group())
            except:
                return None
        return None

    # ---------------------------------------------------------
    # REFINE DESCRIPTION & AC
    # ---------------------------------------------------------
    def _refine_story_text(self, description: str, ac: str):

        prompt = f"""
Clean and improve the following ADO story sections.

Rules:
1. Merge duplicated lines
2. Fix grammar issues
3. Convert raw OCR UI text into readable sentences
4. Keep original meaning
5. Do NOT hallucinate

Return STRICT JSON:
{{
  "description": "...",
  "acceptance_criteria": "..."
}}

DESCRIPTION:
{description}

AC:
{ac}
"""

        try:
            resp = self.openai.chat.completions.create(
                model=self.model,
                messages=[{"role": "user", "content": prompt}],
                temperature=0
            )

            content = resp.choices[0].message.content.strip()
            parsed = self._safe_json_extract(content)

            if parsed:
                return (
                    parsed.get("description", description),
                    parsed.get("acceptance_criteria", ac)
                )

        except Exception as e:
            logger.warning(f"Refinement failed: {e}")

        return description, ac

    # ---------------------------------------------------------
    # CHANNEL DERIVATION
    # ---------------------------------------------------------
    def _derive_channel_context(self, description, ac, channels):

        prompt = f"""
Split story into channel-specific meaning.

Return STRICT JSON:
{{
"RTL": "...",
"WHL": "...",
"DTC": "...",
"CL1": "..."
}}

DESCRIPTION:
{description}

AC:
{ac}
"""

        try:
            resp = self.openai.chat.completions.create(
                model=self.model,
                messages=[{"role": "user", "content": prompt}],
                temperature=0
            )

            content = resp.choices[0].message.content.strip()
            parsed = self._safe_json_extract(content)

            if parsed:
                for ch in channels:
                    parsed.setdefault(ch, "")
                return parsed

        except Exception as e:
            logger.warning(f"Channel derivation failed: {e}")

        return {ch: "" for ch in channels}

    # ---------------------------------------------------------
    # STRUCTURED SUMMARY
    # ---------------------------------------------------------
    def _generate_structured_story_summary(
        self, story_id, title, description, ac, channels
    ):

        prompt = f"""
Generate structured documentation:

User Story {story_id}
Title: {title}

Sections:
- Business Requirement
- UI Field Details
- Channels Impacted
- Acceptance Criteria

DESCRIPTION:
{description}

AC:
{ac}

CHANNELS:
{channels}
"""

        try:
            resp = self.openai.chat.completions.create(
                model=self.model,
                messages=[{"role": "user", "content": prompt}],
                temperature=0
            )

            return resp.choices[0].message.content.strip()

        except:
            return ""

    # ---------------------------------------------------------
    # MAIN ENTRY
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("🚀 ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]
        story = fetch_from_ado(user_story_id)

        description_enriched = process_html_and_download_images(
            story["description"], user_story_id, "description"
        )

        ac_enriched = process_html_and_download_images(
            story["acceptance_criteria"], user_story_id, "ac"
        )

        # 🔥 LLM refinement
        description_enriched, ac_enriched = self._refine_story_text(
            description_enriched, ac_enriched
        )

        channels = detect_channels(ac_enriched)
        channel_rules_map = {ch: build_channel_rules(ch) for ch in channels}

        channel_context_map = self._derive_channel_context(
            description_enriched, ac_enriched, channels
        )

        structured_summary = self._generate_structured_story_summary(
            user_story_id,
            story["title"],
            description_enriched,
            ac_enriched,
            channels
        )

        dump_state_to_txt({
            "user_story_id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
            "channels": channels,
            "channel_context_map": channel_context_map,
            "structured_summary": structured_summary
        })

        state.update({
            "user_story": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
            "channels": channels,
            "channel_rules_map": channel_rules_map,
            "channel_context_map": channel_context_map,
            "structured_summary": structured_summary,
        })

        logger.info("✅ ADO Intelligence Agent completed successfully")

        return state
 =============================\
 import os
import re
import requests
from bs4 import BeautifulSoup
from dotenv import load_dotenv

from utils.image_ocr_processor import extract_text_from_image, clean_ocr_text

load_dotenv()
ADO_PAT = os.getenv("ADO_PAT")


# ---------------------------------------------------------
# DOWNLOAD IMAGE
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
# CLEAN HTML TEXT
# ---------------------------------------------------------
def _normalize_text(text: str) -> str:
    if not text:
        return ""

    text = re.sub(r'(\w)\s{2,}(\w)', r'\1\2', text)
    text = re.sub(r'[ \t]+', ' ', text)
    text = re.sub(r'\n(?=[a-z])', ' ', text)
    text = re.sub(r'\n{2,}', '\n', text)
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

    ocr_lines = []

    for idx, img in enumerate(images, start=1):
        src = img.get("src")
        if not src:
            continue

        save_path = os.path.join(img_folder, f"image_{idx}.png")
        downloaded_path = _download_ado_image(src, save_path)

        if downloaded_path:
            ocr_text = extract_text_from_image(downloaded_path)

            if ocr_text:
                cleaned = clean_ocr_text(ocr_text)

                for line in cleaned.split("\n"):
                    line = line.strip()
                    if line and len(line) > 3:
                        ocr_lines.append(line)

    raw_text = soup.get_text(separator="\n")
    clean_text = _normalize_text(raw_text)

    # Remove duplicate lines already present in HTML
    html_lines = set(clean_text.split("\n"))
    unique_ocr = [l for l in ocr_lines if l not in html_lines]

    if unique_ocr:
        merged = clean_text + "\n\n" + "\n".join(unique_ocr)
    else:
        merged = clean_text

    return merged
=====    ================================================
import pytesseract
import cv2
import os
import re

# Uncomment and adjust path if needed (Windows)
# pytesseract.pytesseract.tesseract_cmd = r"C:\Program Files\Tesseract-OCR\tesseract.exe"


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
# CLEAN OCR TEXT
# ---------------------------------------------------------
def clean_ocr_text(text: str) -> str:
    if not text:
        return ""

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

    text = re.sub(r"[ \t]+", " ", text)
    text = re.sub(r"\n{2,}", "\n", text)

    return text.strip()

        
