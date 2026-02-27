import logging
import base64
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.step_generator import convert_json_to_grouped_steps
from utils.channel_detector import detect_channels
from utils.output_writer import save_final_txt
from config.config import get

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:

    def __init__(self):
        self.client = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )
        self.model = get("CHAT_MODEL")

    # ---------------------------------------------------------
    # Encode image to Base64
    # ---------------------------------------------------------
    def _encode_image(self, path: str) -> str:
        try:
            with open(path, "rb") as f:
                return base64.b64encode(f.read()).decode("utf-8")
        except Exception as e:
            logger.error(f"❌ Failed to encode image {path}: {e}")
            return ""

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("🚀 ADO Intelligence Agent started")

        story_id = state["user_story_id"]

        # -----------------------------------------------------
        # 1️⃣ Fetch ADO Work Item
        # -----------------------------------------------------
        story = fetch_from_ado(story_id)
        logger.info("✅ ADO story fetched successfully")

        raw_description = story.get("description", "")
        raw_ac = story.get("acceptance_criteria", "")

        # -----------------------------------------------------
        # 2️⃣ Process HTML + Download Images (NO OCR)
        # -----------------------------------------------------
        desc_data = process_html_and_download_images(
            raw_description, story_id, "description"
        )

        ac_data = process_html_and_download_images(
            raw_ac, story_id, "acceptance_criteria"
        )

        clean_description = desc_data["clean_text"]
        clean_ac = ac_data["clean_text"]

        # -----------------------------------------------------
        # 3️⃣ Build Vision Input Payload
        # -----------------------------------------------------
        vision_content = [{
            "type": "text",
            "text": f"""
User Story:
{clean_description}

Acceptance Criteria:
{clean_ac}
"""
        }]

        all_images = desc_data["image_paths"] + ac_data["image_paths"]
        logger.info(f"📷 Total images being sent to GPT-4o: {len(all_images)}")

        for img_path in all_images:
            encoded = self._encode_image(img_path)
            if encoded:
                vision_content.append({
                    "type": "image_url",
                    "image_url": {
                        "url": f"data:image/png;base64,{encoded}"
                    }
                })

        # -----------------------------------------------------
        # 4️⃣ Call Azure GPT-4o (Force JSON Output)
        # -----------------------------------------------------
        logger.info("📡 Sending request to Azure GPT-4o")

        response = self.client.chat.completions.create(
            model=self.model,
            temperature=0,
            response_format={"type": "json_object"},
            messages=[
                {
                    "role": "system",
                    "content": """
You are a Senior Mortgage QA Analyst.

Extract structured JSON strictly in this format:

{
  "fields": [
    {
      "name": "",
      "type": "",
      "values": [],
      "location": "",
      "visibilityRule": "",
      "privilegeRestricted": false
    }
  ]
}

Return JSON only.
"""
                },
                {
                    "role": "user",
                    "content": vision_content
                }
            ]
        )

        logger.info("✅ GPT-4o response received")

        llm_output = response.choices[0].message.content

        # -----------------------------------------------------
        # 5️⃣ Convert JSON → Grouped QA Steps
        # -----------------------------------------------------
        grouped_steps = convert_json_to_grouped_steps(llm_output)

        logger.info("📝 Grouped validation steps generated")

        # -----------------------------------------------------
        # 6️⃣ Detect Channels from RAW Acceptance Criteria
        # -----------------------------------------------------
        channels = detect_channels(clean_ac)

        logger.info(f"📡 Channels detected from RAW AC: {channels}")

        # -----------------------------------------------------
        # 7️⃣ Save TXT Output File
        # -----------------------------------------------------
        save_final_txt(
            story_id,
            story.get("title"),
            clean_description,
            grouped_steps
        )

        # -----------------------------------------------------
        # 8️⃣ Update State for Next Agents
        # -----------------------------------------------------
        state["story_id"] = story_id
        state["title"] = story.get("title")
        state["user_story"] = story.get("title")
        state["description"] = clean_description

        # RAW AC (for traceability & retrieval)
        state["original_acceptance_criteria"] = clean_ac

        # FINAL ACCEPTANCE CRITERIA (grouped steps)
        state["acceptance_criteria"] = grouped_steps

        state["llm_output"] = llm_output
        state["channels"] = channels

        logger.info(f"🔎 Final state keys: {list(state.keys())}")
        logger.info("🎯 ADO Intelligence Agent completed successfully")

        return state
