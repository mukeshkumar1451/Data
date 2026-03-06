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
    # Extract keywords from title
    # ---------------------------------------------------------
    def extract_title_keywords(self, title: str) -> List[str]:

        if not title:
            return []

        title = title.lower()

        words = re.findall(r"[a-zA-Z]+(?:\s[a-zA-Z]+)?", title)

        keywords = []

        for w in words:
            if len(w) > 4:
                keywords.append(w.strip())

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
    def find_missing_keywords(self, keywords, description):

        missing = []

        desc_lower = description.lower()

        for k in keywords:
            if k not in desc_lower:
                missing.append(k)

        return missing

    # ---------------------------------------------------------
    # Regenerate description using LLM
    # ---------------------------------------------------------
    def regenerate_description(
        self,
        title,
        testcase,
        missing_keywords
    ):

        prompt = self.review_prompt.format(
            missing_keywords=", ".join(missing_keywords),
            title=title,
            generated_testcase=testcase
        )

        response = self.llm.invoke(prompt)

        return response.content.strip()

    # ---------------------------------------------------------
    # Save review log
    # ---------------------------------------------------------
    def save_log(self, state, log_data):

        os.makedirs("logs", exist_ok=True)

        path = f"logs/{state['user_story_id']}_review_log.json"

        with open(path, "w", encoding="utf-8") as f:
            json.dump(log_data, f, indent=4)

    # ---------------------------------------------------------
    # Main execution
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Review Agent running")

        title_keywords = self.extract_title_keywords(state["title"])

        review_log = {
            "user_story_id": state["user_story_id"],
            "title_keywords": title_keywords,
            "channels": {}
        }

        for channel, testcase in state["llm_outputs"].items():

            description = self.extract_script_description(testcase)

            missing = self.find_missing_keywords(
                title_keywords,
                description
            )

            if missing:

                logger.warning(f"{channel} missing keywords: {missing}")

                updated = self.regenerate_description(
                    state["title"],
                    testcase,
                    missing
                )

                state["llm_outputs"][channel] = updated

                review_log["channels"][channel] = {
                    "missing_keywords": missing,
                    "status": "REGENERATED"
                }

            else:

                review_log["channels"][channel] = {
                    "missing_keywords": [],
                    "status": "PASSED"
                }

        self.save_log(state, review_log)

        return state
