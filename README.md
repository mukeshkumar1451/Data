# utils/html_image_processor.py

import os
import requests
from bs4 import BeautifulSoup
from dotenv import load_dotenv

load_dotenv()
ADO_PAT = os.getenv("ADO_PAT")


def _download_ado_image(url: str, save_path: str) -> str:
    """
    Download image from ADO attachment using PAT authentication.
    """
    try:
        response = requests.get(url, auth=("", ADO_PAT))
        response.raise_for_status()

        with open(save_path, "wb") as f:
            f.write(response.content)

        return save_path
    except Exception as e:
        return f"[Image download failed: {e}]"


def process_html_and_download_images(html: str, story_id: str, section: str) -> str:
    """
    1. Extract images from HTML
    2. Download them locally
    3. Clean HTML text
    4. Append image references into text

    section = 'description' or 'ac'
    """

    if not html:
        return ""

    soup = BeautifulSoup(html, "html.parser")
    images = soup.find_all("img")

    # Folder to save images
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

    # Clean HTML → readable text
    clean_text = soup.get_text(separator="\n")

    final_text = clean_text + "\n\n" + "\n".join(image_references)

    return final_text

