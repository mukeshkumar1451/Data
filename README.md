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

            logger.info(f"Image downloaded → {save_path}")

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

                logger.info(f"Image resized → {resized_path}")

                return resized_path

        except Exception as e:

            logger.error(f"Resize failed: {e}")
            return image_path

    # ------------------------------------------------
    # BASE64 ENCODE
    # ------------------------------------------------
    def encode_image(self, path):

        try:

            with open(path, "rb") as f:
                return base64.b64encode(f.read()).decode("utf-8")

        except Exception as e:

            logger.error(f"Base64 encode failed: {e}")
            return ""

    # ------------------------------------------------
    # ANALYZE IMAGE USING GPT VISION
    # ------------------------------------------------
    def analyze_image(self, image_path, description):

        logger.info(f"Sending image to Vision model → {image_path}")

        encoded = self.encode_image(image_path)

        prompt = f"""
You are a QA analyst analyzing a mortgage LOS UI screenshot.

User Story Context:
{description}

Extract UI elements from the screenshot.

Return ONLY in this format:

Section:
Fields:
Buttons:
Values:

Do not explain anything.
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

        result = response.choices[0].message.content

        logger.info("Vision analysis completed")

        return result

    # ------------------------------------------------
    # PROCESS HTML (TEXT + IMAGE)
    # ------------------------------------------------
    def process_html(self, html, story_id, description):

        soup = BeautifulSoup(html, "html.parser")

        img_folder = os.path.join("downloads", str(story_id))
        os.makedirs(img_folder, exist_ok=True)

        blocks = []
        img_index = 1

        elements = soup.find_all(["p", "li", "img"])

        for element in elements:

            # TEXT BLOCK
            if element.name != "img":

                text = element.get_text(strip=True)

                if text:
                    blocks.append({
                        "type": "text",
                        "value": text
                    })

            # IMAGE BLOCK
            if element.name == "img":

                src = element.get("src")

                logger.info(f"Image detected → {src}")

                if not src:
                    continue

                save_path = os.path.join(
                    img_folder,
                    f"image_{img_index}.png"
                )

                downloaded = self.download_image(src, save_path)

                if not downloaded:
                    continue

                resized = self.resize_image(downloaded)

                try:

                    analysis = self.analyze_image(resized, description)

                    blocks.append({
                        "type": "image_analysis",
                        "value": analysis
                    })

                except Exception as e:

                    logger.error(f"Image analysis failed: {e}")

                img_index += 1

        return blocks

    # ------------------------------------------------
    # CONVERT BLOCKS TO FINAL TEXT
    # ------------------------------------------------
    def blocks_to_text(self, blocks):

        output = []

        for block in blocks:

            if block["type"] == "text":
                output.append(block["value"])

            elif block["type"] == "image_analysis":

                output.append("\n[Image Analysis]\n")
                output.append(block["value"])

        return "\n".join(output)
