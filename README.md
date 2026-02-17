def _parse_llm_output(self, llm_text: str) -> list:
    """
    Returns LIST of testcases (each scenario block)
    """

    testcases = []
    current = None
    step_counter = 1

    def clean_header(text):
        return re.sub(r'[*_ ]+', '', text).lower()

    for raw in llm_text.splitlines():
        line = raw.strip()

        if not line:
            continue

        # ---------------- SCENARIO START ----------------
        if re.match(r'^\**\s*scenario\s*:?', line, re.I):
            if current:
                testcases.append(current)

            current = {
                "scenario": line.split(":",1)[-1].strip(),
                "script": "",
                "requirement": "",
                "steps": []
            }
            step_counter = 1
            continue

        if not current:
            continue

        # ---------------- SCRIPT ----------------
        if re.match(r'^\**\s*script\s*:?', line, re.I):
            current["script"] = line.split(":",1)[-1].strip()
            continue

        # ---------------- REQUIREMENT ----------------
        if re.match(r'^\**\s*requirement\s*:?', line, re.I):
            current["requirement"] = line.split(":",1)[-1].strip()
            continue

        # ---------------- STEP ----------------
        if re.match(r'^step\s*\d+', line, re.I):

            cleaned = re.sub(r'^step\s*\d+\s*\|?', '', line, flags=re.I).strip()

            parts = [p.strip() for p in cleaned.split("|")]

            while len(parts) < 4:
                parts.append("NA")

            desc, screen, data, expected = parts[:4]

            current["steps"].append({
                "step_no": f"Step {step_counter:02d}",
                "desc": desc,
                "screen": screen,
                "data": data,
                "expected": expected,
            })

            step_counter += 1

    if current:
        testcases.append(current)

    return testcases
