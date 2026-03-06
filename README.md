import logging
import os
import json
import re
from typing import Dict, List

from langchain_openai import AzureChatOpenAI
from config.config import get

logger = logging.getLogger(__name__)


class ReviewAgent:

    def __init__(self):

        # Initialize LLM
        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0
        )

        # Load review prompt
        with open("prompts/review_prompt.txt", "r", encoding="utf-8") as f:
            self.review_prompt = f.read()

        logger.info("✅ Review Agent initialized")

    # ---------------------------------------------------------
    # Extract keywords from Title
    # ---------------------------------------------------------
    def extract_title_keywords(self, title: str) -> List[str]:

        if not title:
            return []

        title = title.lower()

        phrases = re.findall(r"[a-zA-Z]+(?:\s[a-zA-Z]+)?", title)

        keywords = []

        for p in phrases:
            if len(p) > 4:
                keywords.append(p.strip())

        return list(set(keywords))

    # ---------------------------------------------------------
    # Extract Test Script Description
    # ---------------------------------------------------------
    def extract_script_description(self, testcase: str) -> str:

        match = re.search(
            r"Test Script Description:(.*?)(?:Pre-Condition|Test Step No)",
            testcase,
            re.DOTALL
        )

        if match:
            return match.group(1).strip()

        return ""

    # ---------------------------------------------------------
    # Find missing keywords
    # ---------------------------------------------------------
    def find_missing_keywords(self, keywords: List[str], description: str):

        missing = []

        description = description.lower()

        for keyword in keywords:
            if keyword not in description:
                missing.append(keyword)

        return missing

    # ---------------------------------------------------------
    # Regenerate testcase using LLM
    # ---------------------------------------------------------
    def regenerate_testcase(
        self,
        state,
        channel,
        testcase,
        missing_keywords
    ):

        logger.info("🔄 Regenerating testcase using historical workflow")

        historical_steps = state["channel_context"][channel]["historical_steps"]

        prompt = self.review_prompt.format(
            missing_keywords=", ".join(missing_keywords),
            title=state["title"],
            description=state["description"],
            ac=state["acceptance_criteria"],
            historical_steps=historical_steps,
            generated_testcase=testcase
        )

        response = self.llm.invoke(prompt)

        return response.content.strip()

    # ---------------------------------------------------------
    # Save testcase TXT log
    # ---------------------------------------------------------
    def save_testcase_log(self, user_story_id, channel, testcase):

        os.makedirs("logs", exist_ok=True)

        file_path = f"logs/{user_story_id}_{channel}_testcase.txt"

        with open(file_path, "w", encoding="utf-8") as f:

            f.write("=====================================\n")
            f.write("ADO INTELLIGENCE ANALYSIS OUTPUT\n")
            f.write("=====================================\n\n")

            f.write(testcase)

        logger.info(f"📄 Saved testcase log → {file_path}")

    # ---------------------------------------------------------
    # Save review JSON log
    # ---------------------------------------------------------
    def save_review_log(self, state, log_data):

        os.makedirs("logs", exist_ok=True)

        file_path = f"logs/{state['user_story_id']}_review_log.json"

        with open(file_path, "w", encoding="utf-8") as f:
            json.dump(log_data, f, indent=4)

        logger.info(f"📄 Saved review log → {file_path}")

    # ---------------------------------------------------------
    # Main Execution
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 Review Agent Running")

        title_keywords = self.extract_title_keywords(state["title"])

        review_log = {
            "user_story_id": state["user_story_id"],
            "title_keywords": title_keywords,
            "channels": {}
        }

        for channel, testcase in state["llm_outputs"].items():

            logger.info(f"🔍 Reviewing channel → {channel}")

            description = self.extract_script_description(testcase)

            missing_keywords = self.find_missing_keywords(
                title_keywords,
                description
            )

            # -------------------------------------------------
            # Missing keywords found → regenerate testcase
            # -------------------------------------------------
            if missing_keywords:

                logger.warning(
                    f"{channel} → Missing keywords: {missing_keywords}"
                )

                updated_testcase = self.regenerate_testcase(
                    state,
                    channel,
                    testcase,
                    missing_keywords
                )

                state["llm_outputs"][channel] = updated_testcase

                final_testcase = updated_testcase

                review_log["channels"][channel] = {
                    "missing_keywords": missing_keywords,
                    "status": "REGENERATED"
                }

            else:

                final_testcase = testcase

                review_log["channels"][channel] = {
                    "missing_keywords": [],
                    "status": "PASSED"
                }

            # -------------------------------------------------
            # Save testcase log for channel
            # -------------------------------------------------
            self.save_testcase_log(
                state["user_story_id"],
                channel,
                final_testcase
            )

        # -----------------------------------------------------
        # Save review summary log
        # -----------------------------------------------------
        self.save_review_log(state, review_log)

        logger.info("✅ Review Agent Completed")

        return state
