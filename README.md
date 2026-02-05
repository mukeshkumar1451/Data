def build_testcase_prompt(
    user_story_id,
    user_story,
    description,
    ac,
    historical_context
):
    return f"""
You are a QA Test Case Designer.

You must generate EXACTLY ONE test case for this User Story.

CRITICAL RULES (DO NOT VIOLATE):

1) Generate ONLY ONE test case.
2) Do NOT create multiple test cases.
3) Do NOT restart step numbering.
4) Steps MUST start from Step 01 and continue sequentially until the end.
5) Do NOT stop early — include ALL required steps.
6) Do NOT explain anything.
7) Output must strictly follow the format below.
8) Every step MUST use "|" separator.

MANDATORY OUTPUT FORMAT:

Scenario: <short scenario>
Script: <script name>
Precondition: <precondition>
Requirement: <requirement mapping>

Step 01 | <step description> | <screen name> | <test data> | <expected result>
Step 02 | <step description> | <screen name> | <test data> | <expected result>
Step 03 | <step description> | <screen name> | <test data> | <expected result>

Continue steps sequentially. Do NOT start a new Scenario.

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

Generate the single consolidated test case now.
"""
