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

        self.channel_rules = get("CHANNEL_RULES")

        logger.info("Review Agent initialized")

    # ---------------------------------------------------------
    # Extract phrases dynamically
    # ---------------------------------------------------------
    def extract_phrases(self, text: str) -> List[str]:

        if not text:
            return []

        text = text.lower()

        phrases = re.findall(r"[a-zA-Z]+(?:\s[a-zA-Z]+){0,3}", text)

        cleaned = []

        for p in phrases:
            if len(p) > 4:
                cleaned.append(p.strip())

        return list(set(cleaned))

    # ---------------------------------------------------------
    # Extract concepts from user story
    # ---------------------------------------------------------
    def extract_story_concepts(self, state):

        concepts = []

        concepts += self.extract_phrases(state["title"])
        concepts += self.extract_phrases(state["description"])
        concepts += self.extract_phrases(state["acceptance_criteria"])

        return list(set(concepts))

    # ---------------------------------------------------------
    # Extract concepts from generated testcase
    # ---------------------------------------------------------
    def extract_testcase_concepts(self, testcase):

        return self.extract_phrases(testcase)

    # ---------------------------------------------------------
    # Find missing concepts
    # ---------------------------------------------------------
    def find_missing(self, story_concepts, testcase_concepts):

        missing = []

        for concept in story_concepts:

            if concept not in testcase_concepts:
                missing.append(concept)

        return missing

    # ---------------------------------------------------------
    # Regenerate testcase using LLM
    # ---------------------------------------------------------
    def regenerate_testcase(
        self,
        state,
        channel,
        testcase,
        missing_items
    ):

        prompt = self.review_prompt.format(
            channel=channel,
            channel_rules=self.channel_rules.get(channel, ""),
            missing_items=", ".join(missing_items),
            title=state["title"],
            description=state["description"],
            ac=state["acceptance_criteria"],
            historical_steps=state["channel_context"][channel]["historical_steps"],
            generated_testcase=testcase
        )

        response = self.llm.invoke(prompt)

        return response.content.strip()

    # ---------------------------------------------------------
    # Save review logs
    # ---------------------------------------------------------
    def save_log(self, state, log_data):

        os.makedirs("logs", exist_ok=True)

        file_path = f"logs/{state['user_story_id']}_review_log.json"

        with open(file_path, "w", encoding="utf-8") as f:
            json.dump(log_data, f, indent=4)

    # ---------------------------------------------------------
    # Main Execution
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Review Agent Running")

        story_concepts = self.extract_story_concepts(state)

        review_log = {
            "user_story_id": state["user_story_id"],
            "story_concepts": story_concepts,
            "channels": {}
        }

        for channel, testcase in state["llm_outputs"].items():

            testcase_concepts = self.extract_testcase_concepts(testcase)

            missing = self.find_missing(
                story_concepts,
                testcase_concepts
            )

            if missing:

                logger.warning(f"{channel} missing concepts: {missing}")

                updated_testcase = self.regenerate_testcase(
                    state,
                    channel,
                    testcase,
                    missing
                )

                state["llm_outputs"][channel] = updated_testcase

                review_log["channels"][channel] = {
                    "missing_concepts": missing,
                    "status": "REGENERATED"
                }

            else:

                review_log["channels"][channel] = {
                    "missing_concepts": [],
                    "status": "PASSED"
                }

        self.save_log(state, review_log)

        return state
