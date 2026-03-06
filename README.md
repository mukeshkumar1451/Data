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

        with open("prompts/review_prompt.txt", "r", encoding="utf-8") as f:
            self.review_prompt = f.read()

        logger.info("Review Agent initialized")

    # ---------------------------------------------------------
    # Extract keywords dynamically from title
    # ---------------------------------------------------------
    def extract_title_keywords(self, title: str) -> List[str]:

        if not title:
            return []

        title = title.lower()

        phrases = re.findall(r"[a-zA-Z]+(?:\s[a-zA-Z]+){0,3}", title)

        keywords = []

        for p in phrases:
            if len(p) > 4:
                keywords.append(p.strip())

        return list(set(keywords))

    # ---------------------------------------------------------
    # Extract words from generated testcase
    # ---------------------------------------------------------
    def extract_testcase_terms(self, testcase: str):

        text = testcase.lower()

        tokens = re.findall(r"[a-zA-Z]+(?:\s[a-zA-Z]+){0,3}", text)

        return list(set(tokens))

    # ---------------------------------------------------------
    # Find missing keywords
    # ---------------------------------------------------------
    def find_missing_keywords(self, keywords, testcase_terms):

        missing = []

        for k in keywords:
            if k not in testcase_terms:
                missing.append(k)

        return missing

    # ---------------------------------------------------------
    # LLM regeneration using historical context
    # ---------------------------------------------------------
    def regenerate_testcase(
        self,
        state,
        channel,
        testcase,
        missing_keywords
    ):

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
    # Save final testcase log
    # ---------------------------------------------------------
    def save_testcase_log(self, story_id, channel, testcase):

        os.makedirs("logs", exist_ok=True)

        path = f"logs/{story_id}_{channel}_testcase.txt"

        with open(path, "w", encoding="utf-8") as f:

            f.write("=====================================\n")
            f.write("ADO INTELLIGENCE ANALYSIS OUTPUT\n")
            f.write("=====================================\n\n")

            f.write(testcase)

        logger.info(f"Saved testcase log: {path}")

    # ---------------------------------------------------------
    # Save review log
    # ---------------------------------------------------------
    def save_review_log(self, state, review_log):

        os.makedirs("logs", exist_ok=True)

        path = f"logs/{state['user_story_id']}_review_log.json"

        with open(path, "w", encoding="utf-8") as f:
            json.dump(review_log, f, indent=4)

    # ---------------------------------------------------------
    # Main execution
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Review Agent running")

        title_keywords = self.extract_title_keywords(state["title"])

        review_log = {
            "user_story_id": state["user_story_id"],
            "keywords": title_keywords,
            "channels": {}
        }

        for channel, testcase in state["llm_outputs"].items():

            testcase_terms = self.extract_testcase_terms(testcase)

            missing = self.find_missing_keywords(
                title_keywords,
                testcase_terms
            )

            if missing:

                logger.warning(f"{channel} missing keywords: {missing}")

                updated_testcase = self.regenerate_testcase(
                    state,
                    channel,
                    testcase,
                    missing
                )

                state["llm_outputs"][channel] = updated_testcase

                final_testcase = updated_testcase

                review_log["channels"][channel] = {
                    "missing_keywords": missing,
                    "status": "REGENERATED"
                }

            else:

                final_testcase = testcase

                review_log["channels"][channel] = {
                    "missing_keywords": [],
                    "status": "PASSED"
                }

            # save testcase log
            self.save_testcase_log(
                state["user_story_id"],
                channel,
                final_testcase
            )

        self.save_review_log(state, review_log)

        return state
