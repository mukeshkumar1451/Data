# utils/html_image_processor.py

import os
import re
import requests
from bs4 import BeautifulSoup
from dotenv import load_dotenv

load_dotenv()
ADO_PAT = os.getenv("ADO_PAT")


# =========================================================
# Download ADO attachment
# =========================================================
def _download_ado_image(url: str, save_path: str) -> str:
    try:
        response = requests.get(url, auth=("", ADO_PAT))
        response.raise_for_status()

        with open(save_path, "wb") as f:
            f.write(response.content)

        return save_path
    except Exception as e:
        return f"[Image download failed: {e}]"


# =========================================================
# TEXT NORMALIZER (CRITICAL FOR AI QUALITY)
# =========================================================
def _normalize_ado_text(text: str) -> str:
    if not text:
        return ""

    # remove excessive spaces
    text = re.sub(r"[ \t]+", " ", text)

    # fix broken OCR words
    text = re.sub(r"\bsh\s+ould\b", "should", text, flags=re.I)
    text = re.sub(r"\bca\s+n\b", "can", text, flags=re.I)
    text = re.sub(r"\bdo\s+es\b", "does", text, flags=re.I)
    text = re.sub(r"\bse\s+lect\b", "select", text, flags=re.I)

    # remove bullet garbage
    text = re.sub(r"--+", ". ", text)
    text = re.sub(r"\*\s*", ". ", text)

    # join broken lines into sentences
    text = re.sub(r"\n+", "\n", text)
    text = re.sub(r"(?<![.!?])\n", ". ", text)

    # remove repeated punctuation
    text = re.sub(r"\.{2,}", ".", text)

    # remove double spaces again
    text = re.sub(r"\s{2,}", " ", text)

    return text.strip()


# =========================================================
# MAIN FUNCTION
# =========================================================
def process_html_and_download_images(html: str, story_id: str, section: str) -> str:
    """
    1. Extract images
    2. Download locally
    3. Clean HTML text
    4. Normalize text for NLP
    5. Append image references
    """

    if not html:
        return ""

    soup = BeautifulSoup(html, "html.parser")
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

        image_references.append(f"[IMAGE DOWNLOADED: {downloaded_path}]")

    # Extract readable text
    raw_text = soup.get_text(separator="\n")

    # 🔥 Normalize text (THIS FIXES CHANNEL DETECTION)
    clean_text = _normalize_ado_text(raw_text)

    # Keep images but separated
    if image_references:
        final_text = clean_text + "\n\n--- ATTACHMENTS ---\n" + "\n".join(image_references)
    else:
        final_text = clean_text

    return final_text
