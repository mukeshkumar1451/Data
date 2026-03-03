import logging
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

    # ---------------------------------------------------------
    # BUILD DYNAMIC PROMPT
    # ---------------------------------------------------------
    def _build_prompt(self, payload: Dict) -> str:

        return f"""
You are a Senior Mortgage QA Analyst.

============================================================
ROLE BEHAVIORAL INTELLIGENCE RULE
============================================================

1. Historical data defines HOW validation is performed.
2. Acceptance Criteria defines WHAT must be validated.
3. You MUST combine both.
4. You MUST NOT copy or restate Acceptance Criteria sentences.
5. Acceptance Criteria is a contract reference only.

If AC sentence structure appears in output, the result is invalid.

============================================================
FLOW REASONING INSTRUCTIONS
============================================================

Analyze historical steps carefully.

From historical steps:
- Detect ordering pattern.
- Detect navigation sequence.
- Detect save cycle pattern.
- Detect audit verification pattern.
- Detect dropdown/checkbox interaction pattern.

From flow_intelligence:
- If audit_behavioral_pattern is True → generate change + save + audit validation.
- If requires_save_cycle is True → include save step after value change.
- If value_transition_pattern is True → validate alternate value as second cycle.
- If dropdown pattern exists → include selection behavior.
- If checkbox pattern exists → include toggle behavior.

Never hardcode behavior.
Use dominance pattern from historical content.

============================================================
AC USAGE RULE
============================================================

Extract validation intent from Acceptance Criteria.
Transform into behavioral enterprise test steps.
Do NOT copy AC text.
Do NOT generate steps starting with "Verify that".
Do NOT rephrase AC directly.

============================================================
OUTPUT FORMAT
============================================================

Each step must follow:
Step XX | Description | Screen Name | Test Data | Expected Result | Requirement Mapping

Expected Result must start with "The system".

============================================================
INPUT DATA
============================================================

User Story ID:
{payload["user_story_id"]}

Title:
{payload["user_story"]}

Description:
{payload["description"]}

Acceptance Criteria:
{payload["ac"]}

Channel:
{payload["channel"]}

Precondition:
{payload["precondition"]}

Historical Steps:
{payload["historical_steps"]}

Flow Intelligence:
{payload["flow_intelligence"]}

============================================================
Generate complete enterprise-grade behavioral test case now.
"""

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("LLM Behavioral Testcase Generation Started")

        outputs = {}

        for channel, ctx in state["channel_context"].items():

            payload = {
                "user_story_id": state["user_story_id"],
                "user_story": state["user_story"],
                "description": state["description"],
                "ac": state["acceptance_criteria"],
                "channel": channel,
                "precondition": ctx.get("precondition", ""),
                "historical_steps": ctx.get("historical_steps", []),
                "flow_intelligence": ctx.get("flow_intelligence", {})
            }

            prompt = self._build_prompt(payload)

            response = self.llm.invoke(prompt)

            outputs[channel] = response.content.strip()

        state["llm_outputs"] = outputs

        logger.info("LLM Behavioral Testcase Generation Completed")

        return state
