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
    # Save Testcase Log
    # ---------------------------------------------------------
    def save_testcase_log(self, story_id, channel, testcase):

        os.makedirs("logs", exist_ok=True)

        file_path = f"logs/{story_id}_{channel}_testcase.txt"

        with open(file_path, "w", encoding="utf-8") as f:
            f.write(testcase)

        logger.info(f"Testcase log saved: {file_path}")

    # ---------------------------------------------------------
    # Save Review Report
    # ---------------------------------------------------------
    def save_review_report(self, story_id, review_text):

        os.makedirs("output", exist_ok=True)

        file_path = f"output/{story_id}_review.txt"

        with open(file_path, "w", encoding="utf-8") as f:
            f.write(review_text)

        logger.info(f"Review report saved: {file_path}")

    # ---------------------------------------------------------
    # Save Prompt for Debugging
    # ---------------------------------------------------------
    def save_prompt(self, story_id, prompt):

        os.makedirs("logs", exist_ok=True)

        file_path = f"logs/{story_id}_review_prompt.txt"

        with open(file_path, "w", encoding="utf-8") as f:
            f.write(prompt)

    # ---------------------------------------------------------
    # MAIN RUN
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Review Agent running")

        story_id = state["user_story_id"]

        # Combine generated testcases
        generated_testcases = "\n\n".join(state["llm_outputs"].values())

        # Combine historical workflow context
        historical_data = ""

        for ch in state["channel_context"]:
            historical_data += (
                f"\n\nChannel: {ch}\n"
                f"Historical Steps:\n"
                f"{state['channel_context'][ch]['historical_steps']}\n"
            )

        # Build prompt
        prompt = self.prompt_template.format(
            title=state["title"],
            description=state["description"],
            acceptance_criteria=state["acceptance_criteria"],
            generated_testcases=generated_testcases,
            historical_data=historical_data
        )

        # Save prompt for debugging
        self.save_prompt(story_id, prompt)

        # Send to LLM
        response = self.llm.invoke([
            {"role": "system", "content": "You are a QA reviewer."},
            {"role": "user", "content": prompt}
        ])

        review_text = response.content.strip()

        # Save review report
        self.save_review_report(story_id, review_text)

        # Save individual testcases per channel
        for channel, testcase in state["llm_outputs"].items():
            self.save_testcase_log(story_id, channel, testcase)

        state["review_report"] = review_text

        return state
