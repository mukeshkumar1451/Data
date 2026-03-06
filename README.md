import os
import re
import base64
import logging
import requests

from bs4 import BeautifulSoup
from PIL import Image
from dotenv import load_dotenv
from openai import AzureOpenAI

from config.config import get

logger = logging.getLogger(__name__)

load_dotenv()

ADO_PAT = os.getenv("ADO_PAT")

MAX_WIDTH = 1200
JPEG_QUALITY = 85


class ImageExtractor:

    def __init__(self):

        self.client = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )

        self.model = get("CHAT_MODEL")

    # ------------------------------------------------
    # CLEAN HTML
    # ------------------------------------------------
    def clean_html(self, html):

        soup = BeautifulSoup(html, "html.parser")

        for tag in soup(["script", "style"]):
            tag.decompose()

        text = soup.get_text(" ")

        text = re.sub(r"\s+", " ", text).strip()

        text = text.replace("s a user", "As a user")

        return text

    # ------------------------------------------------
    # FORMAT DESCRIPTION
    # ------------------------------------------------
    def format_description(self, text):

        text = text.replace(" As a user", "\nAs a user")
        text = text.replace(" I want", "\nI want")
        text = text.replace(" So that", "\nSo that")
        text = text.replace(" Issue #", "\n\nIssue #")
        text = text.replace("-Steps to recreate", "\n\nSteps to recreate")

        return text.strip()

    # ------------------------------------------------
    # FORMAT ACCEPTANCE CRITERIA
    # ------------------------------------------------
    def format_acceptance_criteria(self, text):

        replacements = [
            (" Given ", "\nGiven "),
            (" When ", "\nWhen "),
            (" Then ", "\nThen "),
            (" AND ", "\nAND "),
            (" AC1:", "\nAC1:"),
            (" AC2:", "\n\nAC2:")
        ]

        for old, new in replacements:
            text = text.replace(old, new)

        return text.strip()

    # ------------------------------------------------
    # DOWNLOAD IMAGE
    # ------------------------------------------------
    def download_image(self, url, save_path):

        try:

            response = requests.get(url, auth=("", ADO_PAT))
            response.raise_for_status()

            with open(save_path, "wb") as f:
                f.write(response.content)

            return save_path

        except Exception as e:

            logger.error(f"Image download failed: {e}")
            return ""

    # ------------------------------------------------
    # RESIZE IMAGE
    # ------------------------------------------------
    def resize_image(self, image_path):

        try:

            with Image.open(image_path) as img:

                width, height = img.size

                if width <= MAX_WIDTH:
                    return image_path

                ratio = MAX_WIDTH / float(width)
                new_height = int(height * ratio)

                resized = img.resize((MAX_WIDTH, new_height), Image.LANCZOS)

                base, _ = os.path.splitext(image_path)

                resized_path = base + "_resized.jpg"

                resized.convert("RGB").save(
                    resized_path,
                    "JPEG",
                    quality=JPEG_QUALITY,
                    optimize=True
                )

                return resized_path

        except Exception as e:

            logger.error(f"Resize failed: {e}")
            return image_path

    # ------------------------------------------------
    # BASE64 ENCODE IMAGE
    # ------------------------------------------------
    def encode_image(self, path):

        try:

            with open(path, "rb") as f:
                return base64.b64encode(f.read()).decode("utf-8")

        except Exception as e:

            logger.error(f"Base64 encode failed: {e}")
            return ""

    # ------------------------------------------------
    # ANALYZE IMAGE
    # ------------------------------------------------
    def analyze_image(self, image_path, description):

        encoded = self.encode_image(image_path)

        prompt = f"""
You are a Mortgage QA Analyst.

Analyze the screenshot and extract only UI information related to this story.

User Story:
{description}

Return only:

Section:
Fields:
Buttons:
Values:
"""

        response = self.client.chat.completions.create(
            model=self.model,
            temperature=0,
            messages=[
                {"role": "system", "content": prompt},
                {
                    "role": "user",
                    "content": [
                        {"type": "text", "text": "Analyze screenshot"},
                        {
                            "type": "image_url",
                            "image_url": {
                                "url": f"data:image/jpeg;base64,{encoded}"
                            }
                        }
                    ]
                }
            ]
        )

        return response.choices[0].message.content

    # ------------------------------------------------
    # PROCESS HTML
    # ------------------------------------------------
    def process_html(self, html, story_id):

        soup = BeautifulSoup(html, "html.parser")

        img_folder = os.path.join("downloads", str(story_id))
        os.makedirs(img_folder, exist_ok=True)

        blocks = []
        img_index = 1

        for element in soup.descendants:

            if element.name in ["p", "div", "li", "span"]:

                text = element.get_text(strip=True)

                if text:
                    blocks.append({
                        "type": "text",
                        "value": text
                    })

            elif element.name == "img":

                src = element.get("src")

                if src:

                    save_path = os.path.join(
                        img_folder,
                        f"image_{img_index}.png"
                    )

                    downloaded = self.download_image(src, save_path)

                    if downloaded:

                        blocks.append({
                            "type": "image",
                            "path": downloaded
                        })

                    img_index += 1

        return blocks
=========================================================================
========================================================================
import logging

from ado.ado_client import fetch_from_ado
from utils.image_extractor import ImageExtractor
from utils.output_writer import save_final_txt
from utils.channel_detector import detect_channels

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:

    def __init__(self):

        self.extractor = ImageExtractor()

    def run(self, state: dict):

        logger.info("ADO Intelligence Agent started")

        story_id = state["user_story_id"]

        story = fetch_from_ado(story_id)

        raw_description = story.get("description", "")
        raw_ac = story.get("acceptance_criteria", "")

        # CLEAN DESCRIPTION
        clean_description = self.extractor.clean_html(raw_description)
        clean_description = self.extractor.format_description(clean_description)

        # CLEAN AC
        clean_ac = self.extractor.clean_html(raw_ac)
        clean_ac = self.extractor.format_acceptance_criteria(clean_ac)

        # PROCESS HTML
        blocks = self.extractor.process_html(
            raw_ac,
            story_id
        )

        final_ac = ""

        for block in blocks:

            if block["type"] == "text":

                final_ac += block["value"] + "\n\n"

            elif block["type"] == "image":

                resized = self.extractor.resize_image(block["path"])

                analysis = self.extractor.analyze_image(
                    resized,
                    clean_description
                )

                final_ac += "[Image Analysis]\n"
                final_ac += analysis + "\n\n"

        channels = detect_channels(final_ac)

        save_final_txt(
            story_id,
            story.get("title"),
            clean_description,
            final_ac
        )

        state["story_id"] = story_id
        state["title"] = story.get("title")
        state["description"] = clean_description
        state["acceptance_criteria"] = final_ac
        state["channels"] = channels

        logger.info("ADO Intelligence Agent completed")

        return state
===========================================================
=============================================================
def analyze_image(self, image_path, description):

    encoded = self.encode_image(image_path)

    prompt = f"""
You are a QA Analyst analyzing a UI screenshot.

User Story Context:
{description}

Extract UI elements from the screenshot.

Return ONLY in this format:

Section:
Fields:
Buttons:
Values:

Do not include explanations.
"""

    response = self.client.chat.completions.create(
        model=self.model,
        temperature=0,
        messages=[
            {"role": "system", "content": prompt},
            {
                "role": "user",
                "content": [
                    {"type": "text", "text": "Analyze screenshot"},
                    {
                        "type": "image_url",
                        "image_url": {
                            "url": f"data:image/jpeg;base64,{encoded}"
                        }
                    }
                ]
            }
        ]
    )

    return response.choices[0].message.content
        
        
