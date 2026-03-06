import logging
import os
import re
from typing import Dict, List

from langchain_openai import AzureChatOpenAI
from langchain_core.prompts import PromptTemplate
from config.config import get

logger = logging.getLogger(__name__)


class ReviewAgent:

    def __init__(self):

        prompt_path = get("REVIEW_PROMPT_PATH")

        if not os.path.exists(prompt_path):
            raise FileNotFoundError(f"Review prompt not found: {prompt_path}")

        with open(prompt_path, "r", encoding="utf-8") as f:
            prompt_text = f.read()

        self.prompt = PromptTemplate(
            input_variables=[
                "channel",
                "title",
                "description",
                "ac",
                "historical_steps",
                "testcase",
                "missing_keywords"
            ],
            template=prompt_text
        )

        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0
        )

        self.chain = self.prompt | self.llm

        logger.info("Review Agent initialized")

    # ---------------------------------------------------------
    # Keyword Extraction
    # ---------------------------------------------------------
    def _extract_keywords(self, text: str) -> List[str]:

        text = text.lower()

        text = re.sub(r"[^\w\s]", " ", text)

        words = text.split()

        stop_words = {
            "the","is","a","an","to","of","and","in",
            "when","then","given","should","be","on",
            "for","that","with","from","as","user"
        }

        keywords = [
            w for w in words
            if w not in stop_words and len(w) > 3
        ]

        return list(set(keywords))

    # ---------------------------------------------------------
    # Find Missing Keywords
    # ---------------------------------------------------------
    def _find_missing(self, keywords, testcase):

        testcase = testcase.lower()

        missing = []

        for k in keywords:
            if k not in testcase:
                missing.append(k)

        return missing

    # ---------------------------------------------------------
    # Main Execution
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Review Agent Running")

        story_text = (
            state.get("title","") + " " +
            state.get("description","") + " " +
            state.get("acceptance_criteria","")
        )

        keywords = self._extract_keywords(story_text)

        logger.info(f"Extracted {len(keywords)} keywords from story")

        max_attempts = 3

        for channel, testcase in state["llm_outputs"].items():

            logger.info(f"Reviewing channel → {channel}")

            attempt = 1

            while attempt <= max_attempts:

                missing = self._find_missing(keywords, testcase)

                if not missing:

                    logger.info(
                        f"{channel} → Keyword coverage satisfied"
                    )
                    break

                logger.warning(
                    f"{channel} → Missing keywords: {missing}"
                )

                payload = {
                    "channel": channel,
                    "title": state.get("title",""),
                    "description": state.get("description",""),
                    "ac": state.get("acceptance_criteria",""),
                    "historical_steps": state["channel_context"][channel]["historical_steps"],
                    "testcase": testcase,
                    "missing_keywords": ", ".join(missing)
                }

                result = self.chain.invoke(payload)

                content = result.content.strip()

                if "REVIEW STATUS: PASSED" in content:

                    logger.info(
                        f"{channel} → testcase validated"
                    )
                    break

                if "Corrected Test Case:" in content:

                    testcase = content.split(
                        "Corrected Test Case:"
                    )[-1].strip()

                attempt += 1

            state["llm_outputs"][channel] = testcase

        logger.info("Review Completed")

        return state
