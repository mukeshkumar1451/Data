import logging
import base64
import json
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.image_optimizer import resize_image_if_needed
from utils.image_analyzer import analyze_image_with_llm
from utils.output_writer import save_final_txt
from utils.channel_detector import detect_channels
from config.config import get

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:

    def __init__(self):
        self.client = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )
        self.model = get("CHAT_MODEL")

    def run(self, state: dict) -> dict:

        logger.info("ADO Intelligence Agent started")

        story_id = state["user_story_id"]

        story = fetch_from_ado(story_id)

        raw_description = story.get("description", "")
        raw_ac = story.get("acceptance_criteria", "")

        # DESCRIPTION → plain text only
        clean_description = raw_description

        # AC → text + image blocks
        ac_data = process_html_and_download_images(
            raw_ac, story_id, "acceptance_criteria"
        )

        blocks = ac_data["blocks"]

        final_ac = ""

        for block in blocks:

            if block["type"] == "text":
                final_ac += block["value"] + "\n\n"

            elif block["type"] == "image":

                img_path = resize_image_if_needed(block["path"])

                image_analysis = analyze_image_with_llm(
                    img_path,
                    clean_description
                )

                final_ac += "[Image Analysis]\n"
                final_ac += image_analysis + "\n\n"

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
----------------------------------------------------------------------------------------
import os
import requests
from bs4 import BeautifulSoup
import logging
from dotenv import load_dotenv

logger = logging.getLogger(__name__)

load_dotenv()
ADO_PAT = os.getenv("ADO_PAT")


def _download_ado_image(url: str, save_path: str):

    try:
        response = requests.get(url, auth=("", ADO_PAT))
        response.raise_for_status()

        with open(save_path, "wb") as f:
            f.write(response.content)

        return save_path

    except Exception as e:
        logger.error(f"Image download failed: {e}")
        return ""


def process_html_and_download_images(html: str, story_id: str, section: str):

    soup = BeautifulSoup(html, "html.parser")

    img_folder = os.path.join("downloads", story_id, section)
    os.makedirs(img_folder, exist_ok=True)

    blocks = []
    image_paths = []

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

                downloaded = _download_ado_image(src, save_path)

                if downloaded:
                    image_paths.append(downloaded)

                    blocks.append({
                        "type": "image",
                        "path": downloaded
                    })

                img_index += 1

    return {
        "blocks": blocks,
        "image_paths": image_paths
    }
-------------------------------------------------------------------------
import base64
from openai import AzureOpenAI
from config.config import get


client = AzureOpenAI(
    api_key=get("AZURE_OPENAI_KEY"),
    azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
    api_version=get("AZURE_OPENAI_API_VERSION"),
)

model = get("CHAT_MODEL")


def encode_image(path):

    with open(path, "rb") as f:
        return base64.b64encode(f.read()).decode("utf-8")


def analyze_image_with_llm(image_path, description):

    encoded = encode_image(image_path)

    response = client.chat.completions.create(
        model=model,
        temperature=0,
        messages=[
            {
                "role": "system",
                "content": """
You are a Senior Mortgage QA Analyst.

Analyze the UI screenshot and extract:

• Field names
• Field types (textbox, dropdown, checkbox)
• Dropdown values
• Section location
• Visibility rules
• Privilege restrictions

Focus on UI labels and controls.

Return readable QA information.
"""
            },
            {
                "role": "user",
                "content": [
                    {
                        "type": "text",
                        "text": f"""
User Story Context:
{description}

Analyze the UI screenshot carefully.
"""
                    },
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
