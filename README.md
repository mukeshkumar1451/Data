# ---------------------------------------------------------
# Split AC into channel specific flows
# ---------------------------------------------------------
def _derive_channel_context(self, description: str, ac: str, channels):

    logger.info("🧠 Deriving channel specific flows from story")

    prompt = f"""
You are a mortgage workflow analyst.

Goal:
Split the story into channel specific workflows.

Channel definitions:
RTL → loan officer + borrower actions
WHL → broker submission workflow
DTC → self service borrower portal workflow
CL1 → correspondent purchase workflow

Rules:
- One story may contain multiple workflows
- Assign each workflow to the MOST appropriate channel
- If uncertain → keep minimal text
- NEVER duplicate the full story to all channels

TEXT:
DESCRIPTION:
{description}

AC:
{ac}

Return STRICT JSON ONLY:

{{
  "RTL": "...only RTL related flow...",
  "WHL": "...only WHL related flow...",
  "DTC": "...only DTC related flow...",
  "CL1": "...only CL1 related flow..."
}}
"""

    resp = self.openai.chat.completions.create(
        model=self.model,
        messages=[{"role": "user", "content": prompt}],
        temperature=0
    )

    content = resp.choices[0].message.content.strip()

    try:
        return json.loads(content)
    except Exception:
        logger.warning("⚠️ Flow derivation failed → fallback to full story")
        return {ch: description + "\n" + ac for ch in channels}
