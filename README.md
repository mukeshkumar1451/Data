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
    # BUILD INTELLIGENT MULTI-CLUSTER PROMPT
    # ---------------------------------------------------------
    def _build_prompt(self, payload: Dict) -> str:

        return f"""
You are a Senior Mortgage QA Analyst generating structured enterprise LOS test cases.

============================================================
ARCHITECTURE PRINCIPLE
============================================================

1. Acceptance Criteria defines WHAT must be validated.
2. Historical Workflow Clusters define HOW validation is typically performed.
3. Historical Precondition Candidates define setup structure.
4. You must intelligently combine these.
5. Do NOT copy Acceptance Criteria sentences.
6. Do NOT invent setup values.
7. Do NOT hardcode behavior.

============================================================
WORKFLOW CLUSTERS (INSTITUTIONAL MEMORY)
============================================================

{payload["workflow_clusters"]}

Instructions:
- Analyze all clusters.
- Select cluster(s) that align with validation intent.
- Preserve full ordered sequence of selected cluster.
- If cluster contains modify → save → audit → previous/new validation,
  apply entire behavioral chain.
- Do NOT partially apply cluster in a way that breaks logical order.
- Do NOT apply unrelated cluster patterns.

============================================================
PRECONDITION CANDIDATES
============================================================

{payload["precondition_candidates"]}

Instructions:
- Select the precondition aligned with chosen workflow cluster.
- Prefer preconditions referencing relevant functional area (e.g., DIS, Disclosure, Audit).
- Preserve structured numbered list format.
- Do NOT merge multiple unrelated preconditions.
- Do NOT invent environment URLs or product codes.

============================================================
CHANNEL ENFORCEMENT
============================================================

Channel: {payload["channel"]}

If channel is RTL or DTC:
- Mortgage Broker entities do not exist.
- Do NOT generate broker validation.
- Do NOT mention broker fields.
- Ignore broker-related AC content entirely.

If channel is WHL or CL1:
- Include broker validations only if present in Acceptance Criteria.

============================================================
AC TRANSFORMATION RULE
============================================================

Acceptance Criteria:
{payload["ac"]}

- Extract validation intent only.
- Transform into behavioral enterprise test steps.
- Do NOT start steps with "Verify".
- Do NOT copy AC wording.
- Do NOT produce static-only validation when workflow implies behavioral sequence.

============================================================
SCENARIO & SCRIPT DESCRIPTION RULES
============================================================

Test Scenario Description:
- One sentence.
- Business objective only.
- Max 25 words.
- No technical details.
- No AC sentence reuse.

Test Script Description:
- 2–3 sentences.
- Summarize validation coverage.
- Mention workflow behavior if applicable.
- Do NOT restate AC.

============================================================
CONTEXT
============================================================

User Story:
{payload["user_story"]}

Description:
{payload["description"]}

============================================================
OUTPUT FORMAT (STRICT)
============================================================

Test Case ID / Test Script ID: {payload["user_story_id"]}_{payload["channel"]}_01
Test Scenario Id: {payload["user_story_id"]}_SC_01
Test Scenario Description: <one sentence>
Test Script Description: <2-3 sentences>
Pre-Condition & Assumptions:
<structured numbered precondition>

Then steps strictly as:

Step XX | Description | Screen Name | Test Data | Expected Result | Requirement Mapping

Rules:
- Step 01: Login
- Step 02: Open Loan
- Business steps
- Last step: Logout
- One validation per step
- Expected Result must begin with "The system"
- No markdown
- No notes
- No explanations
- No extra commentary

Generate complete enterprise-grade test case now.
"""

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("LLM Intelligent Testcase Generation Started")

        outputs = {}

        for channel, ctx in state["channel_context"].items():

            payload = {
                "user_story_id": state["user_story_id"],
                "user_story": state["user_story"],
                "description": state["description"],
                "ac": state["acceptance_criteria"],
                "channel": channel,
                "workflow_clusters": ctx.get("workflow_clusters", []),
                "precondition_candidates": ctx.get("precondition_candidates", [])
            }

            prompt = self._build_prompt(payload)

            response = self.llm.invoke(prompt)

            outputs[channel] = response.content.strip()

        state["llm_outputs"] = outputs

        logger.info("LLM Intelligent Testcase Generation Completed")

        return state
