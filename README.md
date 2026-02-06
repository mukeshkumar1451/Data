import re
import requests
import pytesseract
from bs4 import BeautifulSoup
from PIL import Image
from io import BytesIO


def extract_images_from_html(html: str):
    soup = BeautifulSoup(html, "html.parser")
    imgs = soup.find_all("img")
    return [img["src"] for img in imgs if img.get("src")]


def ocr_image_from_url(url: str):
    try:
        response = requests.get(url)
        img = Image.open(BytesIO(response.content))
        text = pytesseract.image_to_string(img)
        return text.strip()
    except Exception as e:
        return f"[OCR Failed: {e}]"


def enrich_acceptance_criteria(ac_html: str):
    print("🧠 Enriching AC with OCR from images...")

    images = extract_images_from_html(ac_html)
    ocr_texts = []

    for url in images:
        print(f"🔍 OCR on image: {url}")
        ocr_texts.append(ocr_image_from_url(url))

    # remove img tags
    soup = BeautifulSoup(ac_html, "html.parser")
    text_only = soup.get_text(separator="\n")

    enriched = text_only + "\n\n--- OCR EXTRACTED TEXT ---\n"
    enriched += "\n".join(ocr_texts)

    return enriched
------------------------------------------
from ac_enricher import enrich_acceptance_criteria

@app.get("/userstory/{story_id}/enriched")
def fetch_user_story_enriched(story_id: str):
    story = get_user_story(story_id)

    enriched_ac = enrich_acceptance_criteria(
        story["acceptance_criteria"]
    )

    story["acceptance_criteria_enriched"] = enriched_ac
    return story
