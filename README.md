import logging

from ado.ado_client import fetch_from_ado
from utils.image_extractor import ImageExtractor
from utils.output_writer import save_final_txt
from utils.channel_detector import detect_channels

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:

    def __init__(self):

        self.extractor = ImageExtractor()

    def run(self, state: dict):

        logger.info("ADO Intelligence Agent started")

        story_id = state["user_story_id"]

        story = fetch_from_ado(story_id)

        raw_description = story.get("description", "")
        raw_ac = story.get("acceptance_criteria", "")

        # CLEAN DESCRIPTION
        clean_description = self.extractor.clean_html(raw_description)
        clean_description = self.extractor.format_description(clean_description)

        # CLEAN AC
        clean_ac = self.extractor.clean_html(raw_ac)
        clean_ac = self.extractor.format_acceptance_criteria(clean_ac)

        # EXTRACT UI KEYWORDS
        keywords = self.extractor.extract_keywords(
            clean_description,
            clean_ac
        )

        logger.info(f"Detected UI Keywords: {keywords}")

        # PROCESS HTML BLOCKS
        blocks = self.extractor.process_html(
            raw_ac,
            story_id
        )

        final_ac = ""

        for block in blocks:

            if block["type"] == "text":

                final_ac += block["value"] + "\n\n"

            elif block["type"] == "image":

                resized = self.extractor.resize_image(block["path"])

                analysis = self.extractor.analyze_image(
                    resized,
                    clean_description,
                    keywords
                )

                final_ac += "[Image Analysis]\n"
                final_ac += analysis + "\n\n"

        channels = detect_channels(final_ac)

        save_final_txt(
            story_id,
            story.get("title"),
            clean_description,
            final_ac
        )

        state["story_id"] = story_id
        state["title"] = story.get("title")
        state["description"] = clean_description
        state["acceptance_criteria"] = final_ac
        state["channels"] = channels
        state["keywords"] = keywords

        logger.info("ADO Intelligence Agent completed")

        return state
