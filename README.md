import re


def parse_llm_steps(llm_text: str):
    """
    Parse LLM output and MERGE ALL steps into ONE SINGLE testcase.
    Handles messy LLM formatting safely.
    """

    scenario = ""
    script = ""
    precondition = ""
    requirement = ""

    steps = []
    step_counter = 1

    for raw in llm_text.splitlines():
        line = raw.strip()

        # ---------------- Header fields ----------------
        if line.lower().startswith("scenario:") and not scenario:
            scenario = line.split(":", 1)[1].strip()

        elif line.lower().startswith("script:") and not script:
            script = line.split(":", 1)[1].strip()

        elif line.lower().startswith("precondition:") and not precondition:
            precondition = line.split(":", 1)[1].strip()

        elif line.lower().startswith("requirement:") and not requirement:
            requirement = line.split(":", 1)[1].strip()

        # ---------------- Step lines ----------------
        elif re.match(r"^step\s*\d+", line.lower()):
            # Normalize weird spacing
            parts = [p.strip() for p in re.split(r"\s*\|\s*", line)]

            if len(parts) >= 5:
                steps.append({
                    "step_no": f"Step {step_counter:02d}",
                    "desc": parts[1],
                    "screen": parts[2],
                    "data": parts[3],
                    "expected": parts[4],
                })
                step_counter += 1

    # Safety: if headers missing, prevent crash
    scenario = scenario or "Generated Test Scenario"
    script = script or "Generated_Test_Script"
    precondition = precondition or ""
    requirement = requirement or ""

    return [{
        "scenario": scenario,
        "script": script,
        "precondition": precondition,
        "requirement": requirement,
        "steps": steps
    }]
