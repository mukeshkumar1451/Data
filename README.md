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

    def __init__(self):
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )
        self.model = get("CHAT_MODEL")

    # ---------------------------------------------------------
    # RULE EXTRACTION (NEW UNIVERSAL LOGIC ENGINE)
    # ---------------------------------------------------------
    def _extract_ui_rules(self, description: str, ac: str) -> str:

        logger.info("🧠 Extracting derived UI rules")

        prompt = f"""
You are a senior mortgage QA analyst.

Extract all visible and logically derivable UI behavior rules.

Return ONLY rule statements in this exact format:

=========== DERIVED UI RULES ===========

Rule 1:
IF <Condition>
THEN <Behavior>

Rule 2:
<Field Name> controls visibility of <Other Field>

Rule 3:
<Field Name> overrides validation

Rules:
- Only include rules supported by the UI.
- If no conditional behavior exists, return:
  "No conditional rules identified."
- Do NOT repeat raw UI text.
- Do NOT hallucinate.
- Be concise.
- No explanation.
- No markdown.

DESCRIPTION:
{description}

AC:
{ac}
"""

        try:
            response = self.openai.chat.completions.create(
                model=self.model,
                messages=[{"role": "user", "content": prompt}],
                temperature=0
            )

            return response.choices[0].message.content.strip()

        except Exception as e:
            logger.error(f"Rule extraction failed: {e}")
            return "Rule extraction failed."

    # ---------------------------------------------------------
    # MAIN ENTRY
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("🚀 ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]

        # 1️⃣ Fetch story
        story = fetch_from_ado(user_story_id)

        # 2️⃣ Process Description (HTML + OCR)
        description_enriched = process_html_and_download_images(
            story["description"],
            user_story_id,
            "description"
        )

        # 3️⃣ Process Acceptance Criteria (NO OCR duplication)
        ac_clean = story["acceptance_criteria"] or ""

        # 4️⃣ Detect Channels (optional — keep if needed)
        channels = detect_channels(ac_clean)

        # 5️⃣ Extract Derived Rules
        derived_rules = self._extract_ui_rules(
            description_enriched,
            ac_clean
        )

        # -------------------------------------------------
        # DEBUG OUTPUT
        # -------------------------------------------------
        print("\n=========== ADO INTELLIGENCE AGENT OUTPUT ===========\n")
        print(f"User Story ID: {user_story_id}")
        print("TITLE:", story["title"])
        print("\n")
        print(derived_rules)
        print("\n=====================================================\n")

        # -------------------------------------------------
        # STATE UPDATE
        # -------------------------------------------------
        state["story"] = {
            "id": user_story_id,
            "title": story["title"],
        }

        state["channels"] = channels
        state["derived_ui_rules"] = derived_rules

        dump_state_to_txt({
            "story_id": user_story_id,
            "title": story["title"],
            "channels": channels,
            "derived_ui_rules": derived_rules
        })

        logger.info("✅ ADO Intelligence Agent completed")

        return state
--------------------------------------------------------------------------------
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
--------------------------------------------------------------------------

