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

    # 1️⃣ Fix broken words: sh ould -> should
    text = re.sub(r'(\w)\s{2,}(\w)', r'\1\2', text)

    # 2️⃣ Remove excessive spaces
    text = re.sub(r'[ \t]+', ' ', text)

    # 3️⃣ Join broken lines but keep paragraphs
    text = re.sub(r'\n(?=[a-z])', ' ', text)
    text = re.sub(r'\n{2,}', '\n', text)

    # 4️⃣ Remove dot noise produced by ADO formatting
    text = re.sub(r'\.\s*\.\s*\.\s*', ' ', text)

    # 5️⃣ Remove space before punctuation
    text = re.sub(r'\s+([.,!?])', r'\1', text)

    # 6️⃣ Normalize colon spacing (important for extractors)
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

        image_references.append(f"[IMAGE DOWNLOADED: {downloaded_path}]")

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
