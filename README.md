====================================================
STRICT FORMATTING ENFORCEMENT (CRITICAL)
====================================================

Output must be PLAIN TEXT only.

Do NOT:
- Use markdown
- Use table formatting
- Use leading pipe "|"
- Use trailing pipe "|"
- Use separator rows (e.g., |-----|)
- Use bold (**)
- Use headings (###)
- Use horizontal rules (---)
- Use bullet points
- Use numbering outside of Step XX format
- Add commentary before or after the script

Each step line MUST:
- Start exactly with: Step XX
- Contain EXACTLY 5 pipe symbols "|"
- Contain EXACTLY 6 columns
- Be written on a single line
- Have no line breaks inside a step
- Not start or end with "|"

The header rows:
- Must not be formatted as markdown
- Must not include extra spacing
- Must not include decorative characters

If formatting rules are violated, regenerate internally before responding.
