prompt = f"""
You are a Mortgage QA Analyst.

Analyze the screenshot ONLY for UI elements related to this story.

User Story:
{description}

Relevant UI keywords:
{keywords}

Return ONLY:

• UI section name
• field names
• button names
• values shown

Do NOT return:
- recommendations
- testing advice
- observations
- explanations

Keep the output concise.
"""
