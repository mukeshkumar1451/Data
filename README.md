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
    # CONVERT TO CLEAR STEP SENTENCES
    # ---------------------------------------------------------
    def _convert_to_steps(self, text: str) -> str:

        logger.info("🧠 Converting extracted content into step sentences")

        text = text[:7000]  # prevent token overflow

        prompt = f"""
You are a senior QA analyst.

Convert the following UI content into clear, simple,
numbered step sentences.

Rules:
- Each step must describe one user action or system behavior.
- Use simple English.
- Use numbered format (1., 2., 3. ...).
- Do NOT repeat raw OCR text.
- Do NOT hallucinate.
- Keep it concise.
- Make it test-case ready.

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
            return text  # fallback to original text

    # ---------------------------------------------------------
    # MAIN ENTRY
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("🚀 ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]

        # 1️⃣ Fetch story from ADO
        story = fetch_from_ado(user_story_id)

        # 2️⃣ Extract description + OCR
        description_enriched = process_html_and_download_images(
            story.get("description", ""),
            user_story_id,
            "description"
        )

        # 3️⃣ Extract AC (without duplicating OCR)
        ac_clean = story.get("acceptance_criteria", "")

        # 4️⃣ Combine both for step conversion
        combined_text = (description_enriched or "") + "\n\n" + (ac_clean or "")

        # 5️⃣ Convert to step sentences
        step_sentences = self._convert_to_steps(combined_text)

        # 6️⃣ Detect channels (optional, keep if needed)
        channels = detect_channels(ac_clean)

        # -------------------------------------------------
        # DEBUG OUTPUT
        # -------------------------------------------------
        print("\n=========== ADO INTELLIGENCE AGENT OUTPUT ===========\n")
        print(f"User Story ID: {user_story_id}")
        print("TITLE:", story.get("title"))
        print("\n")
        print(step_sentences)
        print("\n=====================================================\n")

        # -------------------------------------------------
        # IMPORTANT: Overwrite description & AC
        # -------------------------------------------------
        state["description"] = step_sentences
        state["acceptance_criteria"] = step_sentences

        state["user_story"] = story.get("title")
        state["channels"] = channels

        dump_state_to_txt({
            "story_id": user_story_id,
            "title": story.get("title"),
            "channels": channels,
            "final_description": step_sentences
        })

        logger.info("✅ ADO Intelligence Agent completed")

        return state
