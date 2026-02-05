import re

def parse_llm_steps(llm_text: str):
    lines = llm_text.splitlines()

    scenario = ""
    script = ""
    precondition = ""
    requirement = ""
    steps = []

    for line in lines:
        line = line.strip()

        if line.startswith("Scenario:") and not scenario:
            scenario = line.split(":", 1)[1].strip()

        elif line.startswith("Script:") and not script:
            script = line.split(":", 1)[1].strip()

        elif line.startswith("Precondition:") and not precondition:
            precondition = line.split(":", 1)[1].strip()

        elif line.startswith("Requirement:") and not requirement:
            requirement = line.split(":", 1)[1].strip()

        elif line.startswith("Step"):
            parts = [p.strip() for p in line.split("|")]

            steps.append({
                "step_no": parts[0],
                "action": parts[1] if len(parts) > 1 else "",
                "screen": parts[2] if len(parts) > 2 else "",
                "testdata": parts[3] if len(parts) > 3 else "",
                "expected": parts[4] if len(parts) > 4 else ""
            })

    # 🔥 Always return ONE testcase
    return [{
        "scenario": scenario,
        "script": script,
        "precondition": precondition,
        "requirement": requirement,
        "steps": steps
    }]
