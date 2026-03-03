import logging
import json
from typing import Dict
from langchain_openai import AzureChatOpenAI
from langchain_core.prompts import PromptTemplate
from config.config import get

logger = logging.getLogger(__name__)


class LLMTestcaseGeneratorAgent:

    def __init__(self):

        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0
        )

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
            template=self._build_prompt()
        )

        self.chain = self.prompt | self.llm

    # ---------------------------------------------------------
    # Dynamic Prompt Builder
    # ---------------------------------------------------------
    def _build_prompt(self):

        return """
You are a Senior Mortgage QA Analyst.

Use:
- User Story
- Description
- Acceptance Criteria
- Historical Scenario samples
- Historical Script samples
- Historical Step patterns

Construct a new enterprise-grade test case.

IMPORTANT:
- Do NOT copy historical business logic.
- Use historical data only for structure, tone, and validation depth.
- Dynamically detect dropdowns, checkboxes, pickers, privilege rules, search, pagination, audit, defaulting logic.
- Generate steps accordingly.
- Do NOT assume any behavior not present in story.

============================================================

CHANNEL: {channel}

============================================================

PRECONDITION (REFERENCE ONLY):
{precondition}

============================================================

HISTORICAL STRUCTURE REFERENCES:

Scenario Samples:
{historical_scenario}

Script Samples:
{historical_script}

Step Patterns:
{historical_steps}

============================================================

User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}

============================================================

Generate complete structured test case in plain text format.
"""

    # ---------------------------------------------------------
    # Run
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("LLM Generator Running")

        outputs = {}

        for channel, ctx in state["channel_context"].items():

            payload = {
                "user_story_id": state["user_story_id"],
                "user_story": state["user_story"],
                "description": state["description"],
                "ac": state["acceptance_criteria"],
                "channel": channel,
                "precondition": ctx["precondition"],
                "historical_scenario": json.dumps(ctx["historical_scenarios"], indent=2),
                "historical_script": json.dumps(ctx["historical_scripts"], indent=2),
                "historical_steps": json.dumps(ctx["historical_steps"], indent=2)
            }

            result = self.chain.invoke(payload)
            outputs[channel] = result.content.strip()

        state["llm_outputs"] = outputs

        logger.info("LLM Generator Completed")
        return state
