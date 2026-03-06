import os
import re
import base64
import logging
import requests

from bs4 import BeautifulSoup
from PIL import Image
from dotenv import load_dotenv
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.output_writer import save_final_txt
from utils.channel_detector import detect_channels
from config.config import get

logger = logging.getLogger(__name__)

load_dotenv()

ADO_PAT = os.getenv("ADO_PAT")

MAX_WIDTH = 1200
JPEG_QUALITY = 85


class ADOIntelligenceAgent:

    def __init__(self):

        self.client = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )

        self.model = get("CHAT_MODEL")

    # ---------------------------------------------
    # CLEAN HTML
    # ---------------------------------------------
    def clean_html(self, html):

        soup = BeautifulSoup(html, "html.parser")

        for tag in soup(["script", "style"]):
            tag.decompose()

        return soup.get_text(separator="\n").strip()

    # ---------------------------------------------
    # DOWNLOAD IMAGE FROM ADO
    # ---------------------------------------------
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

    # ---------------------------------------------
    # RESIZE IMAGE
    # ---------------------------------------------
    def resize_image(self, image_path):

        try:

            with Image.open(image_path) as img:

                width, height = img.size

                if width <= MAX_WIDTH:
                    return image_path

                ratio = MAX_WIDTH / float(width)

                new_height = int(height * ratio)

                resized = img.resize(
                    (MAX_WIDTH, new_height),
                    Image.LANCZOS
                )

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

    # ---------------------------------------------
    # BASE64 ENCODE IMAGE
    # ---------------------------------------------
    def encode_image(self, path):

        try:

            with open(path, "rb") as f:

                return base64.b64encode(
                    f.read()
                ).decode("utf-8")

        except Exception as e:

            logger.error(f"Base64 encode failed: {e}")
            return ""

    # ---------------------------------------------
    # EXTRACT STORY KEYWORDS
    # ---------------------------------------------
    def extract_keywords(self, description, ac):

        prompt = f"""
Extract important UI keywords from this story.

Focus on:
- UI sections
- field names
- buttons

Description:
{description}

Acceptance Criteria:
{ac}

Return comma separated keywords.
"""

        response = self.client.chat.completions.create(
            model=self.model,
            temperature=0,
            messages=[{"role": "user", "content": prompt}]
        )

        return response.choices[0].message.content

    # ---------------------------------------------
    # IMAGE ANALYSIS WITH LLM
    # ---------------------------------------------
    def analyze_image(self, image_path, description, keywords):

        encoded = self.encode_image(image_path)

        prompt = f"""
You are a Mortgage QA Analyst.

Analyze this screenshot ONLY for elements related to this story.

User Story:
{description}

Relevant Keywords:
{keywords}

Focus only on relevant UI fields.

Ignore unrelated UI components.
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

    # ---------------------------------------------
    # PROCESS HTML (TEXT + IMAGE POSITION)
    # ---------------------------------------------
    def process_html(self, html, story_id):

        soup = BeautifulSoup(html, "html.parser")

        img_folder = os.path.join(
            "downloads",
            str(story_id)
        )

        os.makedirs(img_folder, exist_ok=True)

        blocks = []
        img_index = 1

        for element in soup.descendants:

            if element.name == "p":

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

                    downloaded = self.download_image(
                        src,
                        save_path
                    )

                    if downloaded:

                        blocks.append({
                            "type": "image",
                            "path": downloaded
                        })

                    img_index += 1

        return blocks

    # ---------------------------------------------
    # MAIN RUN
    # ---------------------------------------------
    def run(self, state):

        logger.info("ADO Intelligence Agent started")

        story_id = state["user_story_id"]

        story = fetch_from_ado(story_id)

        raw_description = story.get("description", "")
        raw_ac = story.get("acceptance_criteria", "")

        clean_description = self.clean_html(raw_description)
        clean_ac = self.clean_html(raw_ac)

        keywords = self.extract_keywords(
            clean_description,
            clean_ac
        )

        logger.info(f"Keywords: {keywords}")

        blocks = self.process_html(
            raw_ac,
            story_id
        )

        final_ac = ""

        for block in blocks:

            if block["type"] == "text":

                final_ac += block["value"] + "\n\n"

            elif block["type"] == "image":

                resized = self.resize_image(
                    block["path"]
                )

                analysis = self.analyze_image(
                    resized,
                    clean_description,
                    keywords
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
