def _convert_to_steps(self, text: str) -> str:

    if not text:
        return ""

    text = text[:6000]

    prompt = f"""
You are a senior QA automation analyst.

Your task is to convert UI content into accurate, test-ready step sentences.

Follow this strict process:

Step 1: Identify valid UI elements only:
- Checkboxes
- Dropdowns
- Buttons
- Toggle fields
- Conditional fields
- System validations

Step 2: Ignore:
- Broken OCR fragments
- Random characters
- Repeated labels
- Decorative text

Step 3: Convert into precise testable sentences.

Formatting Rules:
- Number each step.
- Each step must describe ONE clear behavior.
- Preserve conditional logic when visible.
- Use format:
    1. User can select <Field Name>.
    2. If <Field> is set to <Value>, <Behavior occurs>.
    3. System validates <Condition>.

- Do NOT hallucinate logic.
- Do NOT invent values.
- Do NOT merge multiple behaviors in one sentence.
- Be concise and accurate.

CONTENT:
{text}
"""

    try:
        response = self.openai.chat.completions.create(
            model=self.model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        return response.choices[0].message.content.strip()

    except Exception as e:
        logger.error(f"Step conversion failed: {e}")
        return text
