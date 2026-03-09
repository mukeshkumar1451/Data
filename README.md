import logging
import os
from typing import Dict

from langchain_openai import AzureChatOpenAI
from config.config import get

logger = logging.getLogger(__name__)


class ReviewAgent:

    def __init__(self):

        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0
        )

        with open("prompts/review_prompt.txt", "r", encoding="utf-8") as f:
            self.prompt_template = f.read()

        logger.info("Review Agent initialized")

    # ---------------------------------------------------------
    # Save Testcase Log
    # ---------------------------------------------------------
    def save_log(self, story_id, channel, testcase):

        os.makedirs("logs", exist_ok=True)

        file_path = f"logs/{story_id}_{channel}_testcase.txt"

        with open(file_path, "w", encoding="utf-8") as f:
            f.write(testcase)

        logger.info(f"Testcase log saved: {file_path}")

    # ---------------------------------------------------------
    # Save Review Report
    # ---------------------------------------------------------
    def save_review(self, story_id, review_text):

        os.makedirs("output", exist_ok=True)

        file_path = f"output/{story_id}_test_case_review.txt"

        with open(file_path, "w", encoding="utf-8") as f:
            f.write(review_text)

        logger.info(f"Review report saved: {file_path}")

    # ---------------------------------------------------------
    # MAIN RUN
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Review Agent running")

        story_id = state["user_story_id"]

        # combine generated testcases from all channels
        generated_testcases = "\n\n".join(state["llm_outputs"].values())

        # combine historical context
        historical_data = ""

        for ch in state["channel_context"]:
            historical_data += (
                f"\nChannel: {ch}\n"
                f"{state['channel_context'][ch]['historical_steps']}\n"
            )

        prompt = self.prompt_template.format(
            title=state["title"],
            description=state["description"],
            acceptance_criteria=state["acceptance_criteria"],
            generated_testcases=generated_testcases,
            historical_data=historical_data
        )

        response = self.llm.invoke(prompt)

        review_text = response.content.strip()

        self.save_review(story_id, review_text)

        # save individual testcase logs per channel
        for channel, testcase in state["llm_outputs"].items():
            self.save_log(story_id, channel, testcase)

        state["review_report"] = review_text

        return state
=====================================================================
Role:
You are a QA Reviewer.

Task:
Review generated test cases for completeness, accuracy, and relevance against the provided user story, description, and acceptance criteria.

Instructions:

Contents to review:
• User story title
• User story description
• Acceptance criteria
• Generated test cases
• Historical workflow context

Validation Steps:

1. Context Usage
Verify that historical workflow context is used correctly.

• Navigation flow in test cases should align with historical workflow when applicable.
• Screens referenced in the test cases must exist in the historical workflow context.
• Historical context should be used only for navigation guidance and not to introduce new functionality.

--------------------------------------------------

2. User Story Field Coverage

Fields to validate may appear in BOTH:

• User Story Description
• Acceptance Criteria

Extract all UI fields mentioned in the description and acceptance criteria.


For each field identified:

• Verify that at least one test step validates that field.
• If a field is missing validation in test steps, list it as a gap.


--------------------------------------------------

3. Business Rule Validation

Review test cases to confirm validation of:

• Field presence
• UI location
• Field type (checkbox, dropdown, textbox)
• Dependency logic
• Privilege restrictions

Example validations:

• Mortgage Broker License Type appears when SubPropState = CA
• Mortgage Broker Fee Agreement is privilege restricted

If any rule is not validated in the test cases, mark it as missing.

--------------------------------------------------

4. Test Case Quality Review

Evaluate overall test quality.

Navigation
• Are navigation steps logical and aligned with the LOS workflow?

Field Validation
• Are correct UI fields referenced?
• Are field types correctly described?

Expected Results
• Do expected results clearly describe system behavior?

Test Flow
• Are steps sequential and easy to execute?

Identify:
• Ambiguous steps
• Missing validations
• Incorrect navigation
• Redundant steps

--------------------------------------------------

5. Coverage Calculation

Coverage % = (Number of validated fields / Total fields identified) × 100

--------------------------------------------------

6. Review Status

If all fields and business rules are validated:

REVIEW STATUS: PASSED

If any field or rule is missing validation:

REVIEW STATUS: FAILED

--------------------------------------------------

Output Format

Test Case Review Report

Context Usage Review
<analysis>

Field Coverage Review
<field validation results>

Business Rule Validation
<rule validation results>

Test Case Quality Review
<quality findings>

Coverage Percentage
<calculated value>

Improvement Suggestions
<missing validations>

REVIEW STATUS: PASSED or FAILED

--------------------------------------------------

User Story Title
{title}

Description
{description}

Acceptance Criteria
{acceptance_criteria}

Generated Test Cases
{generated_testcases}

Historical Workflow Context
{historical_data}
