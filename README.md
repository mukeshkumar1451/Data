
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
    # SAVE TESTCASE LOG PER CHANNEL
    # ---------------------------------------------------------
    def save_testcase_log(self, story_id, channel, testcase):

        os.makedirs("logs", exist_ok=True)

        file_path = f"logs/{story_id}_{channel}_testcase.txt"

        with open(file_path, "w", encoding="utf-8") as f:
            f.write(testcase)

        logger.info(f"Testcase log saved: {file_path}")

    # ---------------------------------------------------------
    # SAVE REVIEW REPORT
    # ---------------------------------------------------------
    def save_review_report(self, story_id, review_text):

        os.makedirs("output", exist_ok=True)

        file_path = f"output/{story_id}_review.txt"

        with open(file_path, "w", encoding="utf-8") as f:
            f.write(review_text)

        logger.info(f"Review report saved: {file_path}")

    # ---------------------------------------------------------
    # BUILD HISTORICAL CONTEXT
    # ---------------------------------------------------------
    def build_historical_context(self, state):

        historical_data = ""

        for channel in state.get("channel_context", {}):

            steps = state["channel_context"][channel].get("historical_steps", "")

            historical_data += (
                f"\nChannel: {channel}\n"
                f"Historical Steps:\n"
                f"{steps}\n"
            )

        return historical_data

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Review Agent running")

        story_id = state["user_story_id"]

        # Combine generated testcases
        generated_testcases = "\n\n".join(state["llm_outputs"].values())

        # Historical workflow context
        historical_data = self.build_historical_context(state)

        # Build prompt
        prompt = self.prompt_template.format(
            title=state.get("title", ""),
            description=state.get("description", ""),
            acceptance_criteria=state.get("acceptance_criteria", ""),
            generated_testcases=generated_testcases,
            historical_data=historical_data
        )

        # Send to LLM
        response = self.llm.invoke(prompt)

        review_text = response.content.strip()

        # Save review report
        self.save_review_report(story_id, review_text)

        # ---------------------------------------------------------
        # FINAL TESTCASES (IMPORTANT)
        # ---------------------------------------------------------
        final_testcases = {}

        for channel, testcase in state.get("llm_outputs", {}).items():

            final_testcases[channel] = testcase

            # Save testcase log
            self.save_testcase_log(story_id, channel, testcase)

        # Store outputs in state
        state["review_report"] = review_text
        state["final_testcases"] = final_testcases

        logger.info("Review Agent completed")

        return state
