import re
import base64
import pytesseract
from io import BytesIO
from PIL import Image
from bs4 import BeautifulSoup


def extract_base64_images(html: str):
    """
    Extract base64 images from ADO HTML.
    """
    images = []
    pattern = r'data:image\/png;base64,([A-Za-z0-9+/=]+)'
    matches = re.findall(pattern, html)

    for m in matches:
        img_bytes = base64.b64decode(m)
        images.append(Image.open(BytesIO(img_bytes)))

    return images


def ocr_images(images):
    """
    Run OCR on extracted screenshots.
    """
    ocr_text = []
    for img in images:
        text = pytesseract.image_to_string(img)
        if text.strip():
            ocr_text.append(text.strip())

    return "\n".join(ocr_text)


def clean_html_text(html: str):
    """
    Remove HTML tags and keep readable AC text.
    """
    soup = BeautifulSoup(html, "html.parser")
    text = soup.get_text(separator=" ")
    return " ".join(text.split())


def process_html_and_images(html_content: str) -> str:
    """
    MAIN FUNCTION YOU WERE MISSING

    1. Clean HTML → readable AC text
    2. Extract screenshots → OCR
    3. Merge both into one strong AC text for RAG
    """
    if not html_content:
        return ""

    print("🧹 Cleaning Acceptance Criteria HTML...")
    clean_text = clean_html_text(html_content)

    print("🖼️ Extracting images from AC...")
    images = extract_base64_images(html_content)

    if images:
        print(f"🔍 Running OCR on {len(images)} images...")
        ocr_text = ocr_images(images)
        final_text = clean_text + "\n\nOCR TEXT FROM IMAGES:\n" + ocr_text
    else:
        final_text = clean_text

    return final_text
