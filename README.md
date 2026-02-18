# agents/ado_intelligence_agent.py
import logging
import os
import re
import base64

from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.channel_detector import detect_channels
from utils.state_debugger import dump_state_to_txt
from config.config import get

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:
    """
    Responsibilities:
    1. Fetch User Story from ADO
    2. Clean Description HTML + download images
    3. OCR images (Vision AI)
    4. Inject UI text into story context
    5. Detect Channels
    6. Prepare state for downstream agents
    """

    # ---------------------------------------------------------
    # INIT — Azure Vision Client
    # ---------------------------------------------------------
    def __init__(self):
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )
        self.vision_model = get("CHAT_MODEL")  # GPT-4o (vision capable)

    # ---------------------------------------------------------
    # Encode image → base64
    # ---------------------------------------------------------
    def _encode_image(self, path: str) -> str:
        with open(path, "rb") as f:
            return base64.b64encode(f.read()).decode()

    # ---------------------------------------------------------
    # OCR image using GPT-4o
    # ---------------------------------------------------------
    def _extract_image_text(self, image_path: str) -> str:
        try:
            logger.info(f"🖼 Reading image for OCR: {image_path}")

            base64_img = self._encode_image(image_path)

            resp = self.openai.chat.completions.create(
                model=self.vision_model,
                messages=[
                    {
                        "role": "user",
                        "content": [
                            {
                                "type": "text",
                                "text": """
Extract all visible UI labels, field names, buttons, sections, validation messages.
Return only raw UI text lines.
Do not summarize.
"""
                            },
                            {
                                "type": "image_url",
                                "image_url": {
                                    "url": f"data:image/png;base64,{base64_img}"
                                }
                            }
                        ]
                    }
                ],
                temperature=0
            )

            text = resp.choices[0].message.content.strip()

            logger.info(f"🧠 OCR Extracted: {text[:150]}...")

            return text

        except Exception as e:
            logger.warning(f"⚠️ OCR failed for {image_path}: {e}")
            return ""

    # ---------------------------------------------------------
    # Replace [IMAGE: path] → Inject OCR text
    # ---------------------------------------------------------
    def _inject_image_text(self, enriched_text: str) -> str:

        pattern = r"\[IMAGE:\s*(.*?)\]"

        def replace(match):
            path = match.group(1)

            if not os.path.exists(path):
                return match.group(0)

            ocr_text = self._extract_image_text(path)

            if not ocr_text:
                return match.group(0)

            return f"\n[SCREEN CONTENT]\n{ocr_text}\n"

        return re.sub(pattern, replace, enriched_text)

    # ---------------------------------------------------------
    # Channel Precondition Templates
    # ---------------------------------------------------------
    def _build_preconditions(self):
        return {
            "RTL": """Create a loan from Customer Portal as per pre-conditions below:
1. Channel: RTL
2. Loan Purpose:
3. Loan Type:
4. Product Code:
5. Loan Stage:""",

            "WHL": """Create a loan from Broker Portal as per pre-conditions below:
1. Channel: WHL
2. Loan Purpose:
3. Loan Type:
4. Product Code:
5. Loan Stage:""",

            "DTC": """Create a loan from Ignite Portal as per pre-conditions below:
1. Channel: DTC
2. Loan Purpose: Refinance
3. Loan Type:
4. Product Code:
5. Loan Stage:""",

            "CL1": """Create a loan from Broker Portal as per pre-conditions below:
1. Channel: CL1
2. Loan Purpose:
3. Loan Type:
4. Product Code:
5. Loan Stage:"""
        }

    # ---------------------------------------------------------
    # MAIN ENTRY — LANGGRAPH
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:
        logger.info("🚀 ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]

        # Step 1 — Fetch ADO
        story = fetch_from_ado(user_story_id)

        # Step 2 — Download images
        logger.info("🧹 Processing Description HTML + Images...")
        description_enriched = process_html_and_download_images(
            story["description"], user_story_id, "description"
        )

        logger.info("🧠 Injecting OCR text into Description...")
        description_enriched = self._inject_image_text(description_enriched)

        logger.info("🧹 Processing Acceptance Criteria HTML + Images...")
        ac_enriched = process_html_and_download_images(
            story["acceptance_criteria"], user_story_id, "ac"
        )

        logger.info("🧠 Injecting OCR text into Acceptance Criteria...")
        ac_enriched = self._inject_image_text(ac_enriched)

        # Step 3 — Detect Channels
        channels = detect_channels(ac_enriched)
        logger.info(f"✅ Channels detected: {channels}")

        # Step 4 — Preconditions
        preconditions = self._build_preconditions()

        # Debug print
        print("\n=========== ADO INTELLIGENCE AGENT OUTPUT ===========\n")
        print(f"User Story ID: {user_story_id}")
        print("TITLE:", story["title"])
        print("\nCHANNELS:", channels)
        print("\n=====================================================\n")

        # Dump debug state (IMPORTANT — verify OCR worked)
        dump_state_to_txt({
            "user_story_id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
            "channels": channels
        })

        # -------------------------------------------------
        # Update shared state
        # -------------------------------------------------
        state["user_story"] = story["title"]
        state["description"] = description_enriched
        state["acceptance_criteria"] = ac_enriched
        state["channels"] = channels
        state["preconditions"] = preconditions

        state["story"] = {
            "id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
        }

        return state
