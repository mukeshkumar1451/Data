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

        if not os.path.exists(prompt_path):
            raise FileNotFoundError(f"Prompt file not found: {prompt_path}")

        with open(prompt_path, "r", encoding="utf-8") as f:
            prompt_text = f.read()

        # 🔥 FIXED: Added comma between precondition & historical_steps
        self.prompt = PromptTemplate(
            input_variables=[
                "user_story_id",
                "user_story",
                "description",
                "ac",
                "channel",
                "precondition",
                "historical_scenario",
                "historical_script",
                "historical_steps"
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

        logger.info("LLM Testcase Generator initialized")

    # ---------------------------------------------------------
    # Format Historical Steps for Prompt
    # ---------------------------------------------------------
    def _format_historical_steps(self, steps: list) -> str:

        if not steps:
            return "No historical steps available."

        formatted = ""
        for step in steps:
            formatted += (
                f"{step.get('stepNo', '')} | "
                f"{step.get('description', '')} | "
                f"{step.get('expectedResult', '')}\n"
            )

        return formatted.strip()

    # ---------------------------------------------------------
    # Main Execution
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("LLM Generator Running")

        new_state = dict(state)
        outputs = {}

        for channel, ctx in state["channel_context"].items():

            formatted_steps = self._format_historical_steps(
                ctx.get("historical_steps", [])
            )

            payload = {
                "user_story_id": state["user_story_id"],
                "user_story": state["user_story"],
                "description": state["description"],
                "ac": state["acceptance_criteria"],
                "channel": channel,
                "precondition": ctx.get("precondition", ""),
                "historical_scenario": ctx.get("historical_scenario", ""),
                "historical_script": ctx.get("historical_script", ""),
                "historical_steps": formatted_steps
            }

            result = self.chain.invoke(payload)

            outputs[channel] = result.content.strip()

        new_state["llm_outputs"] = outputs

        logger.info("LLM Testcase Generation Completed")

        return new_state
