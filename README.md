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

        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0
        )

        logger.info("Review Agent initialized")

    # ---------------------------------------------------------
    # Extract keywords ONLY from Title
    # ---------------------------------------------------------
    def extract_title_keywords(self, title: str) -> List[str]:

        if not title:
            return []

        phrases = re.findall(
            r"[A-Z][a-zA-Z]+(?:\s[A-Za-z]+){0,3}",
            title
        )

        return list(set(phrases))

    # ---------------------------------------------------------
    # Find missing keywords in testcase
    # ---------------------------------------------------------
    def find_missing_keywords(self, keywords, testcase):

        testcase_lower = testcase.lower()

        missing = []

        for k in keywords:
            if k.lower() not in testcase_lower:
                missing.append(k)

        return missing

    # ---------------------------------------------------------
    # Regenerate testcase using LLM
    # ---------------------------------------------------------
    def regenerate_testcase(self, state, channel, testcase, missing_keywords):

        logger.info(
            f"{channel} → regenerating testcase due to missing keywords"
        )

        prompt = f"""
Rewrite the testcase below.

Missing keywords from title:
{missing_keywords}

Ensure these keywords are validated in the test steps.

User Story:
{state['title']}

Description:
{state['description']}

Acceptance Criteria:
{state['acceptance_criteria']}

Historical Workflow Reference:
{state['channel_context'][channel]['historical_steps']}

Existing Testcase:
{testcase}

Instructions:
• Maintain navigation flow using historical workflow
• Ensure missing keywords appear in steps
• Keep step numbering correct
• Return only the corrected testcase
"""

        response = self.llm.invoke(prompt)

        return response.content.strip()

    # ---------------------------------------------------------
    # Save review logs
    # ---------------------------------------------------------
    def save_review_log(self, state, review_data):

        log_dir = "logs"
        os.makedirs(log_dir, exist_ok=True)

        file_path = os.path.join(
            log_dir,
            f"{state['user_story_id']}_review_log.json"
        )

        with open(file_path, "w", encoding="utf-8") as f:
            json.dump(review_data, f, indent=4)

    # ---------------------------------------------------------
    # Save final testcase output
    # ---------------------------------------------------------
    def save_testcase_output(self, state, channel, testcase):

        log_dir = "logs"
        os.makedirs(log_dir, exist_ok=True)

        file_path = os.path.join(
            log_dir,
            f"{state['user_story_id']}_{channel}_testcase.txt"
        )

        with open(file_path, "w", encoding="utf-8") as f:
            f.write(testcase)

    # ---------------------------------------------------------
    # Main Execution
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Review Agent Running")

        title = state.get("title", "")

        keywords = self.extract_title_keywords(title)

        logger.info(f"Extracted title keywords: {keywords}")

        review_log = {
            "user_story_id": state["user_story_id"],
            "title_keywords": keywords,
            "channels": {}
        }

        max_attempts = 3

        for channel, testcase in state["llm_outputs"].items():

            logger.info(f"Reviewing channel → {channel}")

            attempt = 1

            initial_missing = []

            while attempt <= max_attempts:

                missing = self.find_missing_keywords(
                    keywords,
                    testcase
                )

                if attempt == 1:
                    initial_missing = missing

                if not missing:

                    logger.info(
                        f"{channel} → all keywords covered"
                    )
                    break

                logger.warning(
                    f"{channel} → missing keywords: {missing}"
                )

                testcase = self.regenerate_testcase(
                    state,
                    channel,
                    testcase,
                    missing
                )

                attempt += 1

            # Save updated testcase
            state["llm_outputs"][channel] = testcase

            # Save testcase output
            self.save_testcase_output(
                state,
                channel,
                testcase
            )

            review_log["channels"][channel] = {
                "attempts": attempt,
                "missing_keywords_initial": initial_missing,
                "review_status": "PASSED" if not missing else "FAILED"
            }

        # Save review log
        self.save_review_log(state, review_log)

        logger.info("Review Completed")

        return state
