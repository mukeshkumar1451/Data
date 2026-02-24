def _generate_structured_story_summary(
    self,
    story_id: str,
    title: str,
    description: str,
    ac: str,
    channels: list
):

    prompt = f"""
You are a mortgage UI documentation analyst.

Your task:
Convert the provided ADO Description + Acceptance Criteria into EXACTLY the following structured format.

IMPORTANT RULES:
- Do NOT hallucinate.
- Do NOT rename UI labels.
- Extract dropdown values exactly as written.
- Extract checkbox labels exactly as written.
- Preserve UI wording.
- Do NOT summarize fields.
- If visibility rule is mentioned (e.g., SubPropState = CA), include it.
- If privilege restriction mentioned, include it.

Return STRICT JSON:

{{
  "document": "FULL FORMATTED DOCUMENT HERE"
}}

FORMAT MUST BE EXACTLY:

User Story {story_id}
Title: {title}

🔹 Business Requirement

(2–3 paragraph explanation derived only from input text)

🔹 UI Field Details and Locations

For each detected UI field:
1️⃣ <Field Short Name>

UI Location:
<If inferable from text>

Field Name:
<Exact label>

Description:
<Dropdown or Checkbox description>

Dropdown Options:
<List each option on new line if applicable>

Associated Controls:
<If any>

⚠ Note:
<If privilege restricted>

⚠ Observations:
<If visibility rule present>

🔹 Channels Impacted

{chr(10).join(channels)}

🔹 Acceptance Criteria

The following fields must be added to Modernized Audit:
<List field names>

Audit must capture:
Field value selected
Timestamp of update
User performing the action

If visibility rules exist, include them under Acceptance Criteria.

INPUT DESCRIPTION:
{description}

INPUT ACCEPTANCE CRITERIA:
{ac}
"""

    try:
        resp = self.openai.chat.completions.create(
            model=self.model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        content = resp.choices[0].message.content.strip()

        # Safe JSON extraction
        import re, json
        match = re.search(r"\{.*\}", content, re.DOTALL)

        if match:
            parsed = json.loads(match.group())
            return parsed.get("document", "")

        return ""

    except Exception as e:
        logger.error(f"Structured summary generation failed: {e}")
        return ""
