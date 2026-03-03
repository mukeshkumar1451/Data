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

    # ---------------------------------------------------------
    # BUILD INTELLIGENT PROMPT
    # ---------------------------------------------------------
    def _build_prompt(self, payload: Dict) -> str:

        return f"""
You are a Senior Mortgage QA Analyst generating enterprise-grade LOS test cases.

============================================================
CORE PRINCIPLE
============================================================

1. Historical data defines HOW validation is performed.
2. Acceptance Criteria defines WHAT must be validated.
3. You MUST combine both.
4. You MUST NOT copy Acceptance Criteria text.
5. You MUST NOT restate AC sentences as steps.
6. AC is a contract reference only.

If AC wording appears directly in output, result is invalid.

============================================================
CHANNEL ENTITY ENFORCEMENT
============================================================

Channel: {payload["channel"]}

If Channel is RTL or DTC:
- DO NOT generate Mortgage Broker related steps.
- DO NOT generate Broker License validations.
- DO NOT generate Broker Compensation logic.
- Ignore broker-related historical patterns completely.

If Channel is WHL or CL1:
- Include Mortgage Broker validations ONLY if present in Acceptance Criteria.
- Privilege validations must be included when applicable.

Violation of channel rule makes output invalid.

============================================================
FLOW INTELLIGENCE
============================================================

Flow Intelligence:
{payload["flow_intelligence"]}

From historical steps:
- Identify navigation order.
- Identify save cycle pattern.
- Identify audit validation pattern.
- Identify dropdown/checkbox interaction pattern.
- Identify value transition behavior.

Rules:

If audit_behavioral_pattern is True:
- Generate value change → save → audit validation flow.

If requires_save_cycle is True:
- Include save step after data modification.

If value_transition_pattern is True:
- Generate alternate value validation cycle.

If has_dropdown_pattern is True:
- Include dropdown selection behavior.

If has_checkbox_pattern is True:
- Include checkbox toggle behavior.

Never invent logic.
Apply only dominant historical patterns.

============================================================
AC TRANSFORMATION RULE
============================================================

Acceptance Criteria:
{payload["ac"]}

Extract validation intent only.

Transform into enterprise behavioral validation steps.

Do NOT:
- Start steps with "Verify that"
- Copy AC sentence structure
- Rephrase AC line-by-line

============================================================
CONTEXT
============================================================

User Story ID:
{payload["user_story_id"]}

Title:
{payload["user_story"]}

Description:
{payload["description"]}

Precondition:
{payload["precondition"]}

Historical Steps:
{payload["historical_steps"]}

============================================================
OUTPUT FORMAT (STRICT)
============================================================

Each step must be written exactly as:

Step XX | Description | Screen Name | Test Data | Expected Result | Requirement Mapping

Rules:
- Sequential numbering
- One validation per step
- Expected Result must start with "The system"
- Login first step
- Open Loan second step
- Logout last step

Generate the complete behavioral test case now.
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
