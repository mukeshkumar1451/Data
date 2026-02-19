
import logging
from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.channel_detector import detect_channels
from utils.state_debugger import dump_state_to_txt
from openai import AzureOpenAI
from config.config import get
import json



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
    def __init__(self):
        # Azure OpenAI
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )

       
        self.model = get("CHAT_MODEL")

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
        logger.info(" ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]

        # Step 1 — Fetch from ADO
        story = fetch_from_ado(user_story_id)

        # Step 2 — Process HTML + Download Images
        logger.info(" Processing Description HTML + Images...")
        description_enriched = process_html_and_download_images(
            story["description"], user_story_id, "description"
        )

        logger.info(" Processing Acceptance Criteria HTML + Images...")
        ac_enriched = process_html_and_download_images(
            story["acceptance_criteria"], user_story_id, "ac"
        )

        # Step 3 — Detect Channels
        channels = detect_channels(ac_enriched)
        logger.info(f" Channels detected: {channels}")

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
        #  CRITICAL — MUTATE STATE (DO NOT REPLACE)
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
    
    # ---------------------------------------------------------
# Split AC into channel specific flows
# ---------------------------------------------------------
def _derive_channel_context(self, description: str, ac: str, channels):

    logger.info("🧠 Deriving channel specific flows from story")

    prompt = f"""
You are a mortgage workflow analyst.

Goal:
Split the story into channel specific workflows.

Channel definitions:
RTL → loan officer + borrower actions
WHL → broker submission workflow
DTC → self service borrower portal workflow
CL1 → correspondent purchase workflow

Rules:
- One story may contain multiple workflows
- Assign each workflow to the MOST appropriate channel
- If uncertain → keep minimal text
- NEVER duplicate the full story to all channels

TEXT:
DESCRIPTION:
{description}

AC:
{ac}

Return STRICT JSON ONLY:

{{
  "RTL": "...only RTL related flow...",
  "WHL": "...only WHL related flow...",
  "DTC": "...only DTC related flow...",
  "CL1": "...only CL1 related flow..."
}}
"""

    resp = self.openai.chat.completions.create(
        model=self.model,
        messages=[{"role": "user", "content": prompt}],
        temperature=0
    )

    content = resp.choices[0].message.content.strip()

    try:
        return json.loads(content)
    except Exception:
        logger.warning("⚠️ Flow derivation failed → fallback to full story")
        return {ch: description + "\n" + ac for ch in channels}
