# agents/ado_intelligence_agent.py
import logging
from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.channel_detector import detect_channels
from utils.state_debugger import dump_state_to_txt



logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:
    """
    Responsibilities:
    1. Fetch User Story from ADO
    2. Clean Description HTML + download images
    3. Clean Acceptance Criteria HTML + download images
    4. Detect Channels from enriched AC
    5. Prepare channel-specific preconditions
    6. Prepare full state for downstream agents (LangGraph-safe)
    """

    # ---------------------------------------------------------
    # Channel Precondition Templates (SYSTEM GENERATED)
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
    # MAIN ENTRY FOR LANGGRAPH
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:
        logger.info("🚀 ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]

        # Step 1 — Fetch from ADO
        story = fetch_from_ado(user_story_id)

        # Step 2 — Process HTML + Download Images
        logger.info("🧹 Processing Description HTML + Images...")
        description_enriched = process_html_and_download_images(
            story["description"], user_story_id, "description"
        )

        logger.info("🧹 Processing Acceptance Criteria HTML + Images...")
        ac_enriched = process_html_and_download_images(
            story["acceptance_criteria"], user_story_id, "ac"
        )

        # Step 3 — Detect Channels
        channels = detect_channels(ac_enriched)
        logger.info(f"✅ Channels detected: {channels}")

        # Step 4 — Build Preconditions
        preconditions = self._build_preconditions()

        # Debug print
        print("\n=========== ADO INTELLIGENCE AGENT OUTPUT ===========\n")
        print(f"User Story ID: {user_story_id}")
        print("TITLE:", story["title"])
        print("\nCHANNELS:", channels)
        print("\n=====================================================\n")

        # Dump debug state
        dump_state_to_txt({
            "user_story_id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
            "channels": channels
        })

        # -------------------------------------------------
        # 🔥 CRITICAL — MUTATE STATE (DO NOT REPLACE)
        # -------------------------------------------------
        state["user_story"] = story["title"]
        state["description"] = description_enriched
        state["acceptance_criteria"] = ac_enriched
        state["channels"] = channels
        state["preconditions"] = preconditions

        # used by LLM + Excel agents
        state["story"] = {
            "id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
        }

        return state
