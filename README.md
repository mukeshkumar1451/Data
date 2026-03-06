import logging
import os
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
            self.prompt_template = f.read()

        logger.info("Review Agent initialized")

    # ---------------------------------------------------------
    # Extract keywords from title
    # ---------------------------------------------------------
    def extract_title_keywords(self, title: str) -> List[str]:

        words = re.findall(r"[A-Za-z]+(?:\s[A-Za-z]+)?", title)

        keywords = []

        for w in words:
            if len(w) > 4:
                keywords.append(w.lower())

        return list(set(keywords))

    # ---------------------------------------------------------
    # Extract testcase words
    # ---------------------------------------------------------
    def extract_testcase_terms(self, testcase):

        text = testcase.lower()

        tokens = re.findall(r"[A-Za-z]+(?:\s[A-Za-z]+)?", text)

        return list(set(tokens))

    # ---------------------------------------------------------
    # Detect missing keywords
    # ---------------------------------------------------------
    def find_missing_keywords(self, keywords, testcase_terms):

        missing = []

        for k in keywords:
            if k not in testcase_terms:
                missing.append(k)

        return missing

    # ---------------------------------------------------------
    # Extract steps from testcase
    # ---------------------------------------------------------
    def extract_steps(self, testcase):

        steps = []

        for line in testcase.split("\n"):
            if line.strip().startswith("Step"):
                steps.append(line.strip())

        return steps

    # ---------------------------------------------------------
    # Call LLM to regenerate
    # ---------------------------------------------------------
    def regenerate(self, testcase, historical_steps, missing_keywords):

        prompt = self.prompt_template.format(
            missing_keywords=", ".join(missing_keywords),
            historical_steps=historical_steps,
            generated_testcase=testcase
        )

        response = self.llm.invoke(prompt)

        return response.content.strip()

    # ---------------------------------------------------------
    # Save testcase logs
    # ---------------------------------------------------------
    def save_testcase(self, story_id, channel, testcase):

        os.makedirs("logs", exist_ok=True)

        file_path = f"logs/{story_id}_{channel}_testcase.txt"

        with open(file_path, "w", encoding="utf-8") as f:

            f.write("=====================================\n")
            f.write("ADO INTELLIGENCE ANALYSIS OUTPUT\n")
            f.write("=====================================\n\n")

            f.write(testcase)

        logger.info(f"Saved testcase log: {file_path}")

    # ---------------------------------------------------------
    # Main run
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Review Agent running")

        title_keywords = self.extract_title_keywords(state["title"])

        for channel, testcase in state["llm_outputs"].items():

            testcase_terms = self.extract_testcase_terms(testcase)

            missing = self.find_missing_keywords(
                title_keywords,
                testcase_terms
            )

            if missing:

                logger.warning(f"{channel} missing keywords: {missing}")

                historical_steps = state["channel_context"][channel]["historical_steps"]

                old_steps = self.extract_steps(testcase)

                updated = self.regenerate(
                    testcase,
                    historical_steps,
                    missing
                )

                new_steps = self.extract_steps(updated)

                # Detect added steps
                added_steps = [s for s in new_steps if s not in old_steps]

                if added_steps:
                    logger.info(f"{channel} → Added Steps:")
                    for s in added_steps:
                        logger.info(s)

                state["llm_outputs"][channel] = updated

                final_testcase = updated

            else:

                final_testcase = testcase

            # Save final testcase
            self.save_testcase(
                state["user_story_id"],
                channel,
                final_testcase
            )

        return state
