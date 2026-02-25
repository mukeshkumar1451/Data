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

        text = text[:6000]  # safety limit

        prompt = f"""
You are a senior QA analyst.

Convert the following content into clear numbered step sentences.

Rules:
- One behavior per sentence.
- Use simple English.
- Do NOT hallucinate.
- Do NOT repeat raw OCR.
- Keep concise.
- Use format:
  1.
  2.
  3.

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
        print("\n--- DESCRIPTION STEPS ---\n")
        print(description_steps)
        print("\n--- ACCEPTANCE CRITERIA STEPS ---\n")
        print(ac_steps)
        print("\n=====================================================\n")

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
