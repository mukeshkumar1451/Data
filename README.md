# ado_client.py - Fetches work item data from Azure DevOps using the REST API.
import os
import requests
from dotenv import load_dotenv


load_dotenv()

ORG = os.getenv("ADO_ORG")
PROJECT = os.getenv("ADO_PROJECT")
PAT = os.getenv("ADO_PAT")

def fetch_from_ado(work_item_id: str):
    url = f"https://dev.azure.com/{ORG}/{PROJECT}/_apis/wit/workitems/{work_item_id}?api-version=7.1"

    response = requests.get(
        url,
        auth=("", PAT)
    )
    response.raise_for_status()

    data = response.json()["fields"]

    # Debug log to inspect the fetched data
    #print(f"Fetched data from ADO: {data}")

    # Validate required fields
    required_fields = ["System.Title", "System.Description", "Microsoft.VSTS.Common.AcceptanceCriteria"]
    
    missing_fields = [field for field in required_fields if field not in data]
    if missing_fields:
        raise KeyError(f"Missing required fields in ADO response: {missing_fields}")
    
    #print(f"Successfully fetched work item {work_item_id} from ADO with title: {data.get('System.Title')} and description  {data.get('System.Description', '')} and acceptance criteria {data.get('Microsoft.VSTS.Common.AcceptanceCriteria', '')}")
    return {
        "id": work_item_id,
        "title": data.get("System.Title", ""),
        "description": data.get("System.Description", ""),
        "acceptance_criteria": data.get("Microsoft.VSTS.Common.AcceptanceCriteria", "")
    }

-----------------------------------------------------------------------------
import logging
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.channel_detector import detect_channels
from utils.state_debugger import dump_state_to_txt
from config.config import get

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:

    def __init__(self):
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )
        self.model = get("CHAT_MODEL")

    # ---------------------------------------------------------
    # Convert any text to step sentences
    # ---------------------------------------------------------
    def _convert_to_steps(self, text: str) -> str:
        if not text:
            return ""

        text = text[:6000]

        prompt = f"""
You are a senior QA automation analyst.

Make into flow Structured sentences that clearly identify user actions, system responses, and conditions.

CONTENT:
{text}
"""

        try:
            response = self.openai.chat.completions.create(
                model=self.model,
                messages=[{"role": "user", "content": prompt}],
                temperature=0
            )

            return response.choices[0].message.content.strip()

        except Exception as e:
            logger.error(f"Step conversion failed: {e}")
            return text


    # ---------------------------------------------------------
    # MAIN
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("🚀 ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]

        # 1️⃣ Fetch story
        story = fetch_from_ado(user_story_id)
        

        raw_description = story.get("description", "")
        raw_ac = story.get("acceptance_criteria", "")

        # 2️⃣ Extract OCR separately
        description_enriched = process_html_and_download_images(
            raw_description,
            user_story_id,
            "description"
        )

        ac_enriched = process_html_and_download_images(
            raw_ac,
            user_story_id,
            "acceptance_criteria"
        )

        # 3️⃣ Convert separately
        description_steps = self._convert_to_steps(description_enriched)
        ac_steps = self._convert_to_steps(ac_enriched)

        # 4️⃣ Detect channels from AC only (recommended)
        channels = detect_channels(ac_enriched)

        # -------------------------------------------------
        # DEBUG OUTPUT
        # -------------------------------------------------
        print("\n=========== ADO INTELLIGENCE AGENT OUTPUT ===========\n")
        print("TITLE:", story.get("title"))
        # print("\n--- DESCRIPTION STEPS ---\n")
        # print(description_steps)
        # print("\n--- ACCEPTANCE CRITERIA STEPS ---\n")
        # print(ac_steps)
        # print("\n=====================================================\n")

        # -------------------------------------------------
        # STATE UPDATE (CLEAN & SEPARATE)
        # -------------------------------------------------
        state["user_story_id"] = user_story_id
        state["title"] = story.get("title")
        state["user_story"] = story.get("title")

        state["description"] = description_steps
        state["acceptance_criteria"] = ac_steps

        state["channels"] = channels

        dump_state_to_txt(state)

        logger.info("✅ ADO Intelligence Agent completed")

        return state
---------------------------------------------------------------------------------------
import os
import re
import requests
from bs4 import BeautifulSoup
from dotenv import load_dotenv

from utils.image_ocr_processor import extract_text_from_image

load_dotenv()
ADO_PAT = os.getenv("ADO_PAT")


# ---------------------------------------------------------
# DOWNLOAD ADO IMAGE
# ---------------------------------------------------------
def _download_ado_image(url: str, save_path: str) -> str:
    try:
        response = requests.get(url, auth=("", ADO_PAT))
        response.raise_for_status()

        with open(save_path, "wb") as f:
            f.write(response.content)

        return save_path
    except Exception as e:
        return ""


# ---------------------------------------------------------
# CLEAN TEXT
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
# MAIN PROCESSOR
# ---------------------------------------------------------

def process_html_and_download_images(html: str, story_id: str, section: str):

    if not html:
        return ""

    soup = BeautifulSoup(html, "html.parser")

    for tag in soup(["script", "style"]):
        tag.decompose()

    images = soup.find_all("img")

    img_folder = os.path.join("downloads", story_id, section)
    os.makedirs(img_folder, exist_ok=True)

    all_ocr_text = []

    for idx, img in enumerate(images, start=1):
        src = img.get("src")
        if not src:
            continue

        save_path = os.path.join(img_folder, f"image_{idx}.png")
        downloaded_path = _download_ado_image(src, save_path)

        if downloaded_path:
            ocr_text = extract_text_from_image(downloaded_path)

            if ocr_text and len(ocr_text.strip()) > 20:
                all_ocr_text.append(ocr_text.strip())

    raw_text = soup.get_text(separator="\n")
    clean_text = _normalize_text(raw_text)

    # Append OCR only once
    if all_ocr_text:
        combined_ocr = "\n".join(set(all_ocr_text))  # deduplicate
        final_text = clean_text + "\n" + combined_ocr
    else:
        final_text = clean_text

    return final_text


----------------------------------------------------------------------
import pytesseract
import cv2
import os
from openai import AzureOpenAI
from config.config import get


# ---------------------------------------------------------
# OCR EXTRACTION
# ---------------------------------------------------------
def extract_text_from_image(image_path: str) -> str:
    try:
        if not os.path.exists(image_path):
            print(f"OCR DEBUG: File not found -> {image_path}")
            return ""

        image = cv2.imread(image_path)

        if image is None:
            print(f"OCR DEBUG: cv2 could not read image -> {image_path}")
            return ""

        gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
        gray = cv2.threshold(gray, 150, 255, cv2.THRESH_BINARY)[1]

        text = pytesseract.image_to_string(gray)

      #  print(f"OCR DEBUG: Extracted text length = {len(text)}")

        return text.strip()

    except Exception as e:
        print(f"OCR ERROR: {e}")
        return ""
    


# ---------------------------------------------------------
# STRUCTURE OCR TEXT INTO UI FIELD FORMAT
# ---------------------------------------------------------
def structure_ocr_text(ocr_text: str) -> str:
    if not ocr_text:
        return ""

    client = AzureOpenAI(
        api_key=get("AZURE_OPENAI_KEY"),
        azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
        api_version=get("AZURE_OPENAI_API_VERSION"),
    )

    model = get("CHAT_MODEL")

    prompt = f"""
You are a senior QA automation analyst.
Your task is to convert raw OCR text from UI screenshots into flow structured sentences that clearly identify user actions, system responses, and conditions.


OCR TEXT:
{ocr_text}
"""

    try:
        resp = client.chat.completions.create(
            model=model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        return resp.choices[0].message.content.strip()

    except Exception as e:
        return f"STRUCTURE_FAILED: {e}"
