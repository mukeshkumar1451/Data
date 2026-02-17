\def _parse_llm_output(self, llm_text: str) -> Dict:

    scenario = ""
    script = ""
    requirement = ""
    steps = []
    step_counter = 1

    GENERIC_WORDS = ["action", "verify", "check", "navigate", "enter", "select"]

    for raw in llm_text.splitlines():
        line = raw.strip()

        if not line:
            continue

        # ---------------- headers ----------------
        if line.lower().startswith("scenario:") and not scenario:
            scenario = line.split(":", 1)[1].strip()
            continue

        if line.lower().startswith("script:") and not script:
            script = line.split(":", 1)[1].strip()
            continue

        if line.lower().startswith("requirement:") and not requirement:
            requirement = line.split(":", 1)[1].strip()
            continue

        # ---------------- steps ----------------
        if re.match(r"^step\s*\d+", line.lower()):

            # remove "Step 01"
            cleaned = re.sub(r"^step\s*\d+\s*", "", line, flags=re.IGNORECASE).strip()

            # split pipe or legacy format
            if "|" in cleaned:
                parts = [p.strip() for p in cleaned.split("|")]
            else:
                parts = [cleaned]

            # remove empties
            parts = [p for p in parts if p]

            if not parts:
                continue

            # ---------------- intelligent column mapping ----------------
            if len(parts) >= 4:

                first = parts[0].lower()

                # LLM inserted verb column → shift left
                if first in GENERIC_WORDS:
                    desc = parts[1]
                    screen = parts[2] if len(parts) > 2 else "NA"
                    data = parts[3] if len(parts) > 3 else "NA"
                    expected = parts[4] if len(parts) > 4 else "Verify system behavior"
                else:
                    desc = parts[0]
                    screen = parts[1] if len(parts) > 1 else "NA"
                    data = parts[2] if len(parts) > 2 else "NA"
                    expected = parts[3] if len(parts) > 3 else "Verify system behavior"

            elif len(parts) == 3:
                desc, screen, data = parts
                expected = "Verify system behavior"

            elif len(parts) == 2:
                desc, screen = parts
                data = "NA"
                expected = "Verify system behavior"

            else:
                desc = parts[0]
                screen = "NA"
                data = "NA"
                expected = "Verify system behavior"

            steps.append({
                "step_no": f"Step {step_counter:02d}",
                "desc": desc,
                "screen": screen,
                "data": data,
                "expected": expected,
            })

            step_counter += 1

    return {
        "scenario": scenario,
        "script": script,
        "requirement": requirement,
        "steps": steps
    }
