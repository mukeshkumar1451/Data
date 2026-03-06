import logging
import os
from typing import Dict

from langchain_openai import AzureChatOpenAI
from langchain_core.prompts import PromptTemplate
from config.config import get

logger = logging.getLogger(__name__)


class LLMTestcaseGeneratorAgent:

    def __init__(self):

        prompt_path = get("PROMPT_TEMPLATE_PATH")
        step_actions_path = get("STEP_ACTIONS_PATH")

        if not os.path.exists(prompt_path):
            raise FileNotFoundError(f"Prompt file not found: {prompt_path}")

        if not os.path.exists(step_actions_path):
            raise FileNotFoundError(f"Step actions file not found: {step_actions_path}")

        # Load main prompt
        with open(prompt_path, "r", encoding="utf-8") as f:
            main_prompt = f.read()

        # Load step rules
        with open(step_actions_path, "r", encoding="utf-8") as f:
            step_rules = f.read()

        full_prompt = f"""
{main_prompt}

------------------------------------------------------------
STEP WRITING RULES
------------------------------------------------------------

{step_rules}
"""

        self.prompt = PromptTemplate(
            input_variables=[
                "user_story_id",
                "title",
                "description",
                "ac",
                "channel",
                "precondition",
                "historical_steps"
            ],
            template=full_prompt
        )

        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0
        )

        self.chain = self.prompt | self.llm

        logger.info("LLM Testcase Generator initialized")


    # ---------------------------------------------------------
    # CHANNEL ENTITY FILTER (BEFORE LLM)
    # ---------------------------------------------------------
    def _filter_historical_by_channel(self, text: str, channel: str) -> str:

        if not text:
            return text

        forbidden_entities = [
            "Mortgage Broker",
            "Broker License",
            "Broker Compensation",
            "Broker Fee Agreement",
            "Mortgage Broker Fee",
            "Mortgage Broker License Type",
            "Manage Broker Disclosures"
        ]

        if channel in ["RTL", "DTC"]:

            filtered_lines = []

            for line in text.splitlines():

                if any(f.lower() in line.lower() for f in forbidden_entities):
                    continue

                filtered_lines.append(line)

            logger.info(f"{channel} → Historical steps filtered for broker entities")

            return "\n".join(filtered_lines)

        return text


    # ---------------------------------------------------------
    # FINAL OUTPUT FILTER (SAFETY)
    # ---------------------------------------------------------
    def _filter_output_by_channel(self, text: str, channel: str) -> str:

        if channel not in ["RTL", "DTC"]:
            return text

        forbidden_entities = [
            "Mortgage Broker",
            "Broker License",
            "Broker Compensation",
            "Broker Fee Agreement",
            "Mortgage Broker License Type",
            "Manage Broker Disclosures"
        ]

        cleaned_lines = []

        for line in text.splitlines():

            if any(f.lower() in line.lower() for f in forbidden_entities):
                continue

            cleaned_lines.append(line)

        logger.info(f"{channel} → Output filtered for broker entities")

        return "\n".join(cleaned_lines)


    # ---------------------------------------------------------
    # LangGraph Entry
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("LLM Generator Running")

        new_state = dict(state)

        outputs = {}

        for channel, ctx in state["channel_context"].items():

            logger.info(f"Generating testcases for channel → {channel}")

            # Filter historical data BEFORE sending to LLM
            filtered_history = self._filter_historical_by_channel(
                ctx.get("historical_steps", ""),
                channel
            )

            payload = {
                "user_story_id": state["user_story_id"],
                "title": state.get("title", ""),
                "description": state.get("description", ""),
                "ac": state.get("acceptance_criteria", ""),
                "channel": channel,
                "precondition": ctx.get("precondition", ""),
                "historical_steps": filtered_history
            }

            result = self.chain.invoke(payload)

            output_text = getattr(result, "content", str(result)).strip()

            # Safety filter AFTER generation
            output_text = self._filter_output_by_channel(
                output_text,
                channel
            )

            outputs[channel] = output_text

        new_state["llm_outputs"] = outputs

        logger.info("LLM Generation Completed")

        return new_state
