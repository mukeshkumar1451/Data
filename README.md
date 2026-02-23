import logging
import json
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.channel_detector import detect_channels
from utils.state_debugger import dump_state_to_txt
from config.config import get
from utils.channel_rules import build_channel_rules

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:
    """
    Responsibilities:
    1. Fetch User Story from ADO
    2. Clean Description HTML + download images
    3. Clean Acceptance Criteria HTML + download images
    4. Detect Channels
    5. 🔥 Split story into channel-specific meaning
    6. Prepare state for retrieval agent
    """

    def __init__(self):
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )
        self.model = get("CHAT_MODEL")

    # ---------------------------------------------------------
    # Channel Context Derivation (MOST IMPORTANT PART)
    # ---------------------------------------------------------
    def _derive_channel_context(self, description: str, ac: str, channels):

        logger.info("🧠 Deriving channel specific flows from story")

        prompt = f"""
You are a mortgage workflow analyst.

Your job:
Split the story into workflow meaning for each mortgage channel.

Channel definitions:
RTL = Loan officer + borrower interaction
WHL = Broker submits package / broker compliance
DTC = Borrower self-service portal behavior
CL1 = Correspondent purchase / post-closing workflow

Rules:
• A story may contain mixed workflows
• Extract only relevant sentences per channel
• DO NOT copy entire story to every channel
• If nothing relevant → return empty string
• Be conservative (precision > recall)

DESCRIPTION:
{description}

AC:
{ac}

Return STRICT JSON:

{{
"RTL": "...",
"WHL": "...",
"DTC": "...",
"CL1": "..."
}}
"""

        try:
            resp = self.openai.chat.completions.create(
                model=self.model,
                messages=[{"role": "user", "content": prompt}],
                temperature=0
            )

            content = resp.choices[0].message.content.strip()
            parsed = json.loads(content)

            # ensure all channels exist
            for ch in channels:
                parsed.setdefault(ch, "")

            logger.info(f"✅ Channel context derived: {parsed}")
            return parsed

        except Exception as e:
            logger.warning(f"⚠️ LLM context split failed → fallback rule mode: {e}")

            # fallback: give full story but still safe
            full_text = description + "\n" + ac
            return {ch: full_text for ch in channels}

    # ---------------------------------------------------------
    # MAIN ENTRY
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:
        logger.info("🚀 ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]

        # 1️⃣ Fetch
        story = fetch_from_ado(user_story_id)

        # 2️⃣ Clean HTML
        logger.info("🧹 Processing Description HTML + Images...")
        description_enriched = process_html_and_download_images(
            story["description"], user_story_id, "description"
        )

        logger.info("🧹 Processing Acceptance Criteria HTML + Images...")
        ac_enriched = process_html_and_download_images(
            story["acceptance_criteria"], user_story_id, "ac"
        )

        # 3️⃣ Detect channels
        channels = detect_channels(ac_enriched)
        channel_rules_map ={
            ch: build_channel_rules(ch) for ch in channels
        }
        logger.info(f"✅ Channels detected: {channels}")

        # 🔥 4️⃣ NEW — derive channel context
        channel_context_map = self._derive_channel_context(
            description_enriched,
            ac_enriched,
            channels
        )

        # Debug output
        print("\n=========== ADO INTELLIGENCE AGENT OUTPUT ===========\n")
        print(f"User Story ID: {user_story_id}")
        print("TITLE:", story["title"])
       # logger.info(f"CHANNEL CONTEXT MAP:\n{json.dumps(channel_context_map, indent=2)}")
        print("\n=====================================================\n")

        dump_state_to_txt({
            "user_story_id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
            "channels": channels,
            "channel_context_map": channel_context_map
        })

        # -------------------------------------------------
        # STATE MUTATION
        # -------------------------------------------------
        state["user_story"] = story["title"]
        state["description"] = description_enriched
        state["acceptance_criteria"] = ac_enriched
        state["channels"] = channels
        state["channel_rules_map"] = channel_rules_map

        # 🔥 NEW DATA FOR RETRIEVAL AGENT
        state["channel_context_map"] = channel_context_map

        state["story"] = {
            "id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
        }

        return state
