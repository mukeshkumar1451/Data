import logging
from typing import Dict

from ado.ado_client import fetch_from_ado
from utils.image_extractor import ImageExtractor
from utils.channel_detector import detect_channels
from utils.output_writer import save_final_txt

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:

    def __init__(self):

        self.extractor = ImageExtractor()

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 ADO Intelligence Agent Started")

        story_id = state["user_story_id"]

        # -----------------------------------------------------
        # Fetch ADO Work Item
        # -----------------------------------------------------
        story = fetch_from_ado(story_id)

        raw_description = story.get("description", "")
        raw_ac = story.get("acceptance_criteria", "")

        # -----------------------------------------------------
        # Clean Description
        # -----------------------------------------------------
        clean_description = self.extractor.clean_html(raw_description)
        clean_description = self.extractor.format_description(clean_description)

        # -----------------------------------------------------
        # Clean Acceptance Criteria
        # -----------------------------------------------------
        clean_ac = self.extractor.clean_html(raw_ac)
        clean_ac = self.extractor.format_acceptance_criteria(clean_ac)

        # -----------------------------------------------------
        # Process HTML Blocks (Text + Images)
        # -----------------------------------------------------
        blocks = self.extractor.process_html(raw_ac, story_id)

        final_ac = ""

        for block in blocks:

            # ---------------------------------------------
            # TEXT BLOCK
            # ---------------------------------------------
            if block["type"] == "text":

                final_ac += block["value"] + "\n\n"

            # ---------------------------------------------
            # IMAGE BLOCK
            # ---------------------------------------------
            elif block["type"] == "image":

                resized = self.extractor.resize_image(block["path"])

                analysis = self.extractor.analyze_image(
                    resized,
                    clean_description
                )

                final_ac += "[Image Analysis]\n"
                final_ac += analysis + "\n\n"

        # -----------------------------------------------------
        # Channel Detection
        # -----------------------------------------------------
        channel_text = f"""
        {clean_description}

        {clean_ac}
        """

        channels = detect_channels(channel_text)

        logger.info(f"Detected channels: {channels}")

        # -----------------------------------------------------
        # Save Output File (optional debugging)
        # -----------------------------------------------------
        save_final_txt(
            story_id,
            story.get("title"),
            clean_description,
            final_ac
        )

        # -----------------------------------------------------
        # Update State for Next Agents
        # -----------------------------------------------------
        state["story_id"] = story_id
        state["title"] = story.get("title")
        state["description"] = clean_description
        state["acceptance_criteria"] = final_ac
        state["channels"] = channels

        logger.info("✅ ADO Intelligence Agent Completed")

        return state
