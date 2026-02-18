# utils/html_image_processor.py

import os
import re
import hashlib
import requests
from bs4 import BeautifulSoup
from dotenv import load_dotenv
from utils.image_text_extractor import extract_ui_knowledge_from_image

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
# Cache vision output (avoid repeated GPT calls)
# ---------------------------------------------------------
def _read_cache(image_path: str):
    cache_dir = "cache/ui_knowledge"
    os.makedirs(cache_dir, exist_ok=True)

    key = hashlib.md5(image_path.encode()).hexdigest()
    cache_file = os.path.join(cache_dir, key + ".txt")

    if os.path.exists(cache_file):
        with open(cache_file, "r", encoding="utf-8") as f:
            return f.read()

    return None


def _write_cache(image_path: str, data: str):
    cache_dir = "cache/ui_knowledge"
    os.makedirs(cache_dir, exist_ok=True)

    key = hashlib.md5(image_path.encode()).hexdigest()
    cache_file = os.path.join(cache_dir, key + ".txt")

    with open(cache_file, "w", encoding="utf-8") as f:
        f.write(data)


# ---------------------------------------------------------
# Clean extracted text (VERY IMPORTANT FOR LLM)
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
# MAIN FUNCTION
# ---------------------------------------------------------
def process_html_and_download_images(html: str, story_id: str, section: str) -> str:
    """
    Extract readable text + download images + understand UI screenshots

    section = 'description' or 'ac'
    """

    if not html:
        return ""

    soup = BeautifulSoup(html, "html.parser")

    for tag in soup(["script", "style"]):
        tag.decompose()

    # -------------------------------------------------
    # IMAGE DOWNLOAD + UNDERSTANDING
    # -------------------------------------------------
    images = soup.find_all("img")

    img_folder = os.path.join("downloads", story_id, section)
    os.makedirs(img_folder, exist_ok=True)

    image_references = []
    image_knowledge_blocks = []

    for idx, img in enumerate(images, start=1):
        src = img.get("src")
        if not src:
            continue

        save_path = os.path.join(img_folder, f"image_{idx}.png")
        downloaded_path = _download_ado_image(src, save_path)

        image_references.append(f"[IMAGE DOWNLOADED: {downloaded_path}]")

        # -------------------------------
        # 🔥 Vision Knowledge Extraction
        # -------------------------------
        if os.path.exists(downloaded_path):

            cached = _read_cache(downloaded_path)

            if cached:
                knowledge = cached
            else:
                knowledge = extract_ui_knowledge_from_image(downloaded_path)
                _write_cache(downloaded_path, knowledge)

            image_knowledge_blocks.append(knowledge)

    # -------------------------------------------------
    # TEXT EXTRACTION
    # -------------------------------------------------
    raw_text = soup.get_text(separator="\n")
    clean_text = _normalize_text(raw_text)

    # -------------------------------------------------
    # FINAL ENRICHED TEXT (VERY IMPORTANT)
    # -------------------------------------------------
    final_parts = [clean_text]

    if image_references:
        final_parts.append("\n".join(image_references))

    if image_knowledge_blocks:
        final_parts.append("\n===== UI LOGIC EXTRACTED FROM SCREENSHOTS =====\n")
        final_parts.append("\n".join(image_knowledge_blocks))

    final_text = "\n\n".join(final_parts)

    return final_text
