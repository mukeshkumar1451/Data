import pytesseract
import cv2
import os
from openai import AzureOpenAI
from config.config import get


# ---------------------------------------------------------
# OCR EXTRACTION
# ---------------------------------------------------------
def extract_text_from_image(image_path: str) -> str:
    try:
        image = cv2.imread(image_path)

        if image is None:
            return ""

        gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
        gray = cv2.threshold(gray, 150, 255, cv2.THRESH_BINARY)[1]

        text = pytesseract.image_to_string(gray)

        return text.strip()

    except Exception as e:
        return f"OCR_FAILED: {e}"


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
=================================================================
import os
import re
import requests
from bs4 import BeautifulSoup
from dotenv import load_dotenv

from utils.image_ocr_processor import extract_text_from_image, structure_ocr_text

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
def process_html_and_download_images(html: str, story_id: str, section: str) -> str:

    if not html:
        return ""

    soup = BeautifulSoup(html, "html.parser")

    for tag in soup(["script", "style"]):
        tag.decompose()

    images = soup.find_all("img")

    img_folder = os.path.join("downloads", story_id, section)
    os.makedirs(img_folder, exist_ok=True)

    structured_blocks = []

    # -------------------------------------------------
    # DOWNLOAD + OCR + STRUCTURE
    # -------------------------------------------------
    for idx, img in enumerate(images, start=1):
        src = img.get("src")
        if not src:
            continue

        save_path = os.path.join(img_folder, f"image_{idx}.png")
        downloaded_path = _download_ado_image(src, save_path)

        if downloaded_path:
            ocr_text = extract_text_from_image(downloaded_path)
            structured_text = structure_ocr_text(ocr_text)

            if structured_text:
                structured_blocks.append(structured_text)

    raw_text = soup.get_text(separator="\n")
    clean_text = _normalize_text(raw_text)

    if structured_blocks:
        final_text = clean_text + "\n\n=== EXTRACTED FROM IMAGES ===\n\n" + "\n\n".join(structured_blocks)
    else:
        final_text = clean_text

    return final_text
    -----------------------------------------------------------------
    def _generate_structured_story_summary(
    self,
    story_id: str,
    title: str,
    description: str,
    ac: str,
    channels: list
) -> str:

    prompt = f"""
Generate structured business documentation in EXACT format:

User Story {story_id}
Title: {title}

🔹 Business Requirement
(2–3 paragraph summary)

🔹 UI Field Details and Locations
Include:
- Field Name
- UI Location
- Description
- Dropdown options
- Restrictions
- Logic conditions

🔹 Channels Impacted
List channels exactly as provided.

🔹 Acceptance Criteria
Structured bullets including:
- Fields to audit
- Audit capture requirements
- Visibility rules
- Channel consistency

DESCRIPTION:
{description}

AC:
{ac}

CHANNELS:
{channels}

Do not hallucinate.
Use only given information.
"""

    resp = self.openai.chat.completions.create(
        model=self.model,
        messages=[{"role": "user", "content": prompt}],
        temperature=0
    )

    return resp.choices[0].message.content.strip()
    ---------------------------------------------------------------
    
        
