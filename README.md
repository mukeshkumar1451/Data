def build_testcase_prompt(
    user_story_id,
    user_story,
    description,
    ac,
    historical_context
):
    return f"""
You are a QA Test Case Designer.

You must generate NEW test cases for the given User Story by learning from the Historical Test Cases.

STRICT RULES (CRITICAL — DO NOT VIOLATE):

1) Do NOT explain anything.
2) Do NOT add headings, notes, markdown, or comments.
3) Output ONLY test case content in the exact format below.
4) Every test step MUST use pipe "|" separator.
5) Steps MUST start from "Step 01" and increment sequentially.
6) Generate COMPLETE steps. Do NOT stop early.
7) This output will be parsed directly into Excel columns.

Required Output Format (MANDATORY):

Scenario: <short scenario>
Script: <script name>
Precondition: <precondition>
Requirement: <requirement mapping>

Step 01 | <step description> | <screen name> | <test data> | <expected result>
Step 02 | <step description> | <screen name> | <test data> | <expected result>
Step 03 | <step description> | <screen name> | <test data> | <expected result>

Repeat the same structure if multiple test cases are required.

Guidelines for generation:

- Learn the writing style from Historical Test Cases.
- Cover positive, negative, and edge scenarios.
- Use realistic screen names and test data from history.
- Keep steps detailed, actionable, and sequential.
- Do NOT invent unrelated functionality.
- Do NOT shorten steps.
- Do NOT skip navigation steps.

------------------------------------------------------------

USER STORY ID:
{user_story_id}

USER STORY:
{user_story}

DESCRIPTION:
{description}

ACCEPTANCE CRITERIA:
{ac}

------------------------------------------------------------

HISTORICAL TEST CASES FOR LEARNING STYLE:
{historical_context}

------------------------------------------------------------

Now generate the new test cases.
"""
