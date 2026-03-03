import logging
import os
from typing import Dict
from langchain_openai import AzureChatOpenAI
from langchain_core.prompts import PromptTemplate
from config.config import get
import json

logger = logging.getLogger(__name__)


class LLMTestcaseGeneratorAgent:

    def __init__(self):

        prompt_path = get("PROMPT_TEMPLATE_PATH")

        if not os.path.exists(prompt_path):
            raise FileNotFoundError(f"Prompt file not found: {prompt_path}")

        with open(prompt_path, "r", encoding="utf-8") as f:
            prompt_text = f.read()

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
                "historical_steps",
                "flow_intelligence"
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
    # Analyze Title for Enhancement Pattern
    # ---------------------------------------------------------
    def _detect_enhancement_flag(self, title: str) -> bool:
        keywords = ["addition", "enhancement", "update", "modernized"]
        title_lower = title.lower()
        return any(k in title_lower for k in keywords)

    # ---------------------------------------------------------
    # Main Execution
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("LLM Generator Running")

        new_state = dict(state)
        outputs = {}

        story_title = state.get("user_story", "")
        enhancement_flag = self._detect_enhancement_flag(story_title)

        for channel, ctx in state["channel_context"].items():

            formatted_steps = self._format_historical_steps(
                ctx.get("historical_steps", [])
            )

            # Flow intelligence from Retrieval Agent (optional)
            flow_intelligence = state.get("flow_intelligence", {})

            payload = {
                "user_story_id": state["user_story_id"],
                "user_story": story_title,
                "description": state["description"],
                "ac": state["acceptance_criteria"],
                "channel": channel,
                "precondition": ctx.get("precondition", ""),
                "historical_scenario": ctx.get("historical_scenario", ""),
                "historical_script": ctx.get("historical_script", ""),
                "historical_steps": formatted_steps,
                "flow_intelligence": json.dumps({
                    "enhancement_flag": enhancement_flag,
                    "dominant_selection": flow_intelligence.get("dominant_selection", {}),
                    "dominant_step_pattern": flow_intelligence.get("dominant_step_pattern", []),
                    "dependency_patterns": flow_intelligence.get("dependency_patterns", [])
                }, indent=2)
            }

            result = self.chain.invoke(payload)
            outputs[channel] = result.content.strip()

        new_state["llm_outputs"] = outputs

        logger.info("LLM Testcase Generation Completed")
        return new_state
