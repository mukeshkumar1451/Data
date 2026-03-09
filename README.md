import logging
import os
from typing import Dict

from langchain_openai import AzureChatOpenAI
from config.config import get

logger = logging.getLogger(__name__)


class ReviewAgent:

    def __init__(self):

        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0
        )

        with open("prompts/review_prompt.txt", "r", encoding="utf-8") as f:
            self.prompt_template = f.read()

        logger.info("Review Agent initialized")

    # ---------------------------------------------------------
    # Save Review Report
    # ---------------------------------------------------------
    def save_review(self, story_id, review_text):

        os.makedirs("output", exist_ok=True)

        file_path = f"output/{story_id}_test_case_review.txt"

        with open(file_path, "w", encoding="utf-8") as f:
            f.write(review_text)

        logger.info(f"Review report saved: {file_path}")

    # ---------------------------------------------------------
    # MAIN RUN
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Review Agent running")

        story_id = state["user_story_id"]

        # combine generated testcases from all channels
        generated_testcases = "\n\n".join(state["llm_outputs"].values())

        # combine historical context
        historical_data = ""

        for ch in state["channel_context"]:
            historical_data += (
                f"\nChannel: {ch}\n"
                f"{state['channel_context'][ch]['historical_steps']}\n"
            )

        prompt = self.prompt_template.format(
            user_story=state["user_story"],
            description=state["description"],
            acceptance_criteria=state["acceptance_criteria"],
            generated_testcases=generated_testcases,
            historical_data=historical_data
        )

        response = self.llm.invoke(prompt)

        review_text = response.content.strip()

        self.save_review(story_id, review_text)

        state["review_report"] = review_text

        return state
