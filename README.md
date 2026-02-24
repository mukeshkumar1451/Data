def _refine_story_text(self, description: str, ac: str) -> tuple:

    prompt = f"""
Clean and improve the following ADO story sections.

1. Merge duplicated lines
2. Fix grammar issues
3. Convert raw UI OCR text into readable sentences
4. Keep original meaning
5. Do NOT hallucinate new content

Return STRICT JSON:

{{
  "description": "...",
  "acceptance_criteria": "..."
}}

DESCRIPTION:
{description}

AC:
{ac}
"""

    try:
        resp = self.openai.chat.completions.create(
            model=self.model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        content = resp.choices[0].message.content.strip()
        return json.loads(content).values()

    except:
        return description, ac
