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

        phrases = re.findall(r"[A-Za-z]+(?:\s[A-Za-z]+)?", title)

        keywords = []

        for p in phrases:
            if len(p) > 4:
                keywords.append(p.lower())

        return list(set(keywords))

    # ---------------------------------------------------------
    # Extract Test Step Descriptions
    # ---------------------------------------------------------
    def extract_step_descriptions(self, testcase: str):

        steps = []

        for line in testcase.split("\n"):

            if line.strip().startswith("Step"):

                parts = line.split("|")

                if len(parts) > 1:
                    steps.append(parts[1].strip().lower())

        return steps

    # ---------------------------------------------------------
    # Find missing keywords
    # ---------------------------------------------------------
    def find_missing_keywords(self, keywords, step_desc):

        text = " ".join(step_desc)

        missing = []

        for k in keywords:
            if k not in text:
                missing.append(k)

        return missing

    # ---------------------------------------------------------
    # Extract step lines
    # ---------------------------------------------------------
    def extract_steps(self, testcase):

        steps = []

        for line in testcase.split("\n"):
            if line.strip().startswith("Step"):
                steps.append(line.strip())

        return steps

    # ---------------------------------------------------------
    # Match historical steps for missing keywords
    # ---------------------------------------------------------
    def find_historical_match(self, historical_steps, missing_keywords):

        matched_steps = []

        logger.info("\nSearching historical steps...\n")

        for keyword in missing_keywords:

            for line in historical_steps.split("\n"):

                if keyword in line.lower():

                    logger.info(f"Keyword: {keyword}")
                    logger.info(f"Matched Historical Step: {line.strip()}")

                    matched_steps.append(line.strip())

                    break

        return "\n".join(matched_steps)

    # ---------------------------------------------------------
    # Save testcase logs
    # ---------------------------------------------------------
    def save_log(self, story_id, channel, testcase):

        os.makedirs("logs", exist_ok=True)

        file_path = f"logs/{story_id}_{channel}_testcase.txt"

        with open(file_path, "w", encoding="utf-8") as f:

            f.write("=====================================\n")
            f.write("ADO INTELLIGENCE ANALYSIS OUTPUT\n")
            f.write("=====================================\n\n")
            f.write(testcase)

        logger.info(f"Saved testcase log: {file_path}")

    # ---------------------------------------------------------
    # Call LLM to regenerate testcase
    # ---------------------------------------------------------
    def call_llm(self, testcase, historical_steps, missing_keywords):

        logger.info("\nSending to LLM with:\n")
        logger.info(f"Missing Keywords: {missing_keywords}")
        logger.info(f"Historical Flow:\n{historical_steps}")

        prompt = self.prompt_template.format(
            missing_keywords=", ".join(missing_keywords),
            historical_steps=historical_steps,
            generated_testcase=testcase
        )

        response = self.llm.invoke(prompt)

        return response.content.strip()

    # ---------------------------------------------------------
    # MAIN RUN
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Review Agent running")

        title = state["title"]

        keywords = self.extract_title_keywords(title)

        logger.info(f"Extracted Title Keywords: {keywords}")

        for channel, testcase in state["llm_outputs"].items():

            logger.info(f"\nReviewing channel → {channel}\n")

            step_desc = self.extract_step_descriptions(testcase)

            missing_keywords = self.find_missing_keywords(
                keywords,
                step_desc
            )

            if not missing_keywords:

                logger.info(f"{channel} → No missing keywords")

                self.save_log(
                    state["user_story_id"],
                    channel,
                    testcase
                )

                continue

            logger.warning(
                f"{channel} missing keywords: {missing_keywords}"
            )

            historical_steps = state["channel_context"][channel]["historical_steps"]

            matched_historical = self.find_historical_match(
                historical_steps,
                missing_keywords
            )

            old_steps = self.extract_steps(testcase)

            updated_testcase = self.call_llm(
                testcase,
                matched_historical,
                missing_keywords
            )

            new_steps = self.extract_steps(updated_testcase)

            added_steps = [s for s in new_steps if s not in old_steps]

            if added_steps:

                logger.info(f"\n{channel} → Added Steps:")

                for step in added_steps:
                    logger.info(step)

            state["llm_outputs"][channel] = updated_testcase

            self.save_log(
                state["user_story_id"],
                channel,
                updated_testcase
            )

        return state
