import logging
import json
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
    3. Clean Acceptance Criteria HTML + download images
    4. Detect Channels
    5. 🔥 Derive channel specific flows (NEW)
    6. Prepare state for downstream agents
    """

    # ---------------------------------------------------------
    # INIT
    # ---------------------------------------------------------
    def __init__(self):
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )

        self.model = get("CHAT_MODEL")

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
    # 🔥 NEW — FLOW DERIVATION
    # ---------------------------------------------------------
    def _derive_channel_context(self, description: str, ac: str, channels):

        logger.info("🧠 Deriving channel specific workflows...")

        prompt = f"""
You are a mortgage workflow analyst.

Split the story into channel specific workflows.

Channel definitions:
RTL → loan officer + borrower interaction
WHL → broker submission workflow
DTC → borrower self service portal
CL1 → correspondent purchase workflow

Rules:
- One story may contain multiple workflows
- Assign each workflow to BEST matching channel
- DO NOT duplicate full story to all channels
- Keep only relevant sentences

TEXT:
DESCRIPTION:
{description}

AC:
{ac}

Return STRICT JSON ONLY:

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

            logger.info("✅ Channel workflows derived successfully")
            return parsed

        except Exception as e:
            logger.warning(f"⚠️ Flow derivation failed: {e}")
            return {ch: description + "\n" + ac for ch in channels}

    # ---------------------------------------------------------
    # MAIN ENTRY
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:
        logger.info("🚀 ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]

        # Step 1 — Fetch
        story = fetch_from_ado(user_story_id)

        # Step 2 — Process HTML
        logger.info("🧹 Processing Description...")
        description_enriched = process_html_and_download_images(
            story["description"], user_story_id, "description"
        )

        logger.info("🧹 Processing Acceptance Criteria...")
        ac_enriched = process_html_and_download_images(
            story["acceptance_criteria"], user_story_id, "ac"
        )

        # Step 3 — Detect channels
        channels = detect_channels(ac_enriched)
        logger.info(f"📡 Channels detected: {channels}")

        # 🔥 Step 4 — Derive channel flows
        channel_context = self._derive_channel_context(
            description_enriched,
            ac_enriched,
            channels
        )

        # Step 5 — Preconditions
        preconditions = self._build_preconditions()

        # Debug
        print("\n=========== ADO INTELLIGENCE AGENT OUTPUT ===========\n")
        print(f"User Story ID: {user_story_id}")
        print("TITLE:", story["title"])
        print("CHANNELS:", channels)
        print("CHANNEL CONTEXT:", channel_context)
        print("\n=====================================================\n")

        # Dump debug
        dump_state_to_txt({
            "user_story_id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
            "channels": channels,
            "channel_context": channel_context
        })

        # -------------------------------------------------
        # MUTATE STATE
        # -------------------------------------------------
        state["user_story"] = story["title"]
        state["description"] = description_enriched
        state["acceptance_criteria"] = ac_enriched
        state["channels"] = channels
        state["preconditions"] = preconditions
        state["channel_context"] = channel_context  # ⭐ NEW IMPORTANT

        state["story"] = {
            "id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
        }

        return state
