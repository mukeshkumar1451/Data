def _parse_llm_output(self, llm_text: str) -> Dict:

    steps = []
    step_counter = 1
    current_step = None

    for raw in llm_text.splitlines():
        line = raw.strip()

        if not line:
            continue

        # Detect Step line
        if re.search(r"step\s*\d+", line.lower()):

            if current_step:
                steps.append(current_step)

            current_step = {
                "step_no": f"Step {step_counter:02d}",
                "desc": "",
                "screen": "NA",
                "data": "NA",
                "expected": "Verify system behavior"
            }

            step_counter += 1
            continue

        if current_step:

            if "description" in line.lower():
                current_step["desc"] = line.split(":", 1)[-1].strip()

            elif "screen" in line.lower():
                current_step["screen"] = line.split(":", 1)[-1].strip()

            elif "test data" in line.lower():
                current_step["data"] = line.split(":", 1)[-1].strip()

            elif "expected" in line.lower():
                current_step["expected"] = line.split(":", 1)[-1].strip()

    if current_step:
        steps.append(current_step)

    return {
        "scenario": "",
        "script": "",
        "requirement": "",
        "steps": steps
    }
