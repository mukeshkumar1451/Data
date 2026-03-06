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
            raise FileNotFoundError(f"Step Actions file not found: {step_actions_path}")

        with open(prompt_path, "r", encoding="utf-8") as f:
            main_prompt = f.read()

        with open(step_actions_path, "r", encoding="utf-8") as f:
            step_rules = f.read()

        # Combine both prompts
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
    # LangGraph Entry
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("LLM Generator Running")

        new_state = dict(state)
        outputs = {}

        for channel, ctx in state["channel_context"].items():

            payload = {
                "user_story_id": state["user_story_id"],
                "title": state.get("title", ""),
                "description": state.get("description", ""),
                "ac": state.get("acceptance_criteria", ""),
                "channel": channel,
                "precondition": ctx.get("precondition", ""),
                "historical_steps": ctx.get("historical_steps", "")
            }

            logger.info(f"Generating testcases for channel → {channel}")

            result = self.chain.invoke(payload)

            output_text = getattr(result, "content", str(result)).strip()

            outputs[channel] = output_text

        new_state["llm_outputs"] = outputs

        logger.info("LLM Generation Completed")

        return new_state
