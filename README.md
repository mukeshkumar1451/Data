import logging
from typing import Dict
from langchain_openai import AzureChatOpenAI
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

    def _build_prompt(self, payload: Dict) -> str:

        return f"""
You are a Senior Mortgage QA Analyst.

Acceptance Criteria defines validation scope.
Historical workflow defines behavioral sequence.

Do NOT copy AC wording.
Transform validation intent into enterprise workflow steps.

Apply the following dominant workflow pattern:

{payload["workflow_pattern_summary"]}

Navigation Sequence Observed:
{payload["navigation_sequence"]}

Dominant Step Ordering:
{payload["dominant_step_ordering"]}

User Story:
{payload["user_story"]}

Description:
{payload["description"]}

Acceptance Criteria:
{payload["ac"]}

Channel:
{payload["channel"]}

Precondition:
{payload["precondition"]}

Output format strictly:

Step XX | Description | Screen Name | Test Data | Expected Result | Requirement Mapping

No markdown.
No notes.
No extra text.
Login first.
Logout last.
"""

    def run(self, state: Dict) -> Dict:

        outputs = {}

        for channel, ctx in state["channel_context"].items():

            payload = {
                "user_story": state["user_story"],
                "description": state["description"],
                "ac": state["acceptance_criteria"],
                "channel": channel,
                "precondition": ctx.get("precondition", ""),
                "workflow_pattern_summary": ctx.get("workflow_pattern_summary", ""),
                "navigation_sequence": ctx.get("navigation_sequence", []),
                "dominant_step_ordering": ctx.get("dominant_step_ordering", [])
            }

            prompt = self._build_prompt(payload)

            response = self.llm.invoke(prompt)

            outputs[channel] = response.content.strip()

        state["llm_outputs"] = outputs
        return state
