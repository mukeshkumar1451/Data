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

        text = soup.get_text("\n")

        text = re.sub(r"\n\s*\n", "\n\n", text)
        text = re.sub(r"[ \t]+", " ", text)

        return text.strip()

    # ------------------------------------------------
    # FORMAT DESCRIPTION
    # ------------------------------------------------
    def format_description(self, text):

        text = text.replace(" As a user", "\nAs a user")
        text = text.replace(" I want", "\nI want")
        text = text.replace(" So that", "\nSo that")

        return text.strip()

    # ------------------------------------------------
    # FORMAT ACCEPTANCE CRITERIA
    # ------------------------------------------------
    def format_acceptance_criteria(self, text):

        replacements = [
            (" Given ", "\nGiven "),
            (" When ", "\nWhen "),
            (" Then ", "\nThen "),
            (" And ", "\nAnd "),
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
    # ANALYZE IMAGE WITH LLM
    # ------------------------------------------------
    def analyze_image(self, image_path, description, acceptance_criteria):

        encoded = self.encode_image(image_path)

        prompt = f"""
You are a QA automation analyst analyzing a mortgage application UI screenshot.

User Story Description:
{description}

Acceptance Criteria:
{acceptance_criteria}

Your task:
Extract UI elements visible in the screenshot that relate to the acceptance criteria.

Focus especially on these fields if present:
- HPML
- Intent to Proceed
- Mortgage Broker Fee/Compensation Agreement
- Mortgage Broker License Type

Instructions:

1. Identify the SECTION name.
2. Extract the fields under that section.
3. Capture the exact UI label text.
4. Identify field type:
   - Dropdown
   - Checkbox
   - Textbox
5. For dropdowns list all visible values.
6. For checkboxes return the state: Checked or Unchecked.
7. Identify any buttons visible.

Do NOT hallucinate fields not present in the screenshot.

Return strictly in this format:

=====================================
IMAGE ANALYSIS OUTPUT
=====================================

Section: <Section Name>

Fields:

1. <Field Name>
   UI Label:
   <Label>

   Field Type:
   <Dropdown / Checkbox / Textbox>

   Values:
   - <Option>

Buttons:
- <Button Name>

=====================================
END OF IMAGE ANALYSIS
=====================================
"""

        response = self.client.chat.completions.create(
            model=self.model,
            temperature=0,
            messages=[
                {"role": "system", "content": prompt},
                {
                    "role": "user",
                    "content": [
                        {
                            "type": "text",
                            "text": "Analyze the UI screenshot and extract fields."
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

                        resized = self.resize_image(downloaded)

                        blocks.append({
                            "type": "image",
                            "path": resized
                        })

                    img_index += 1

        return blocks
