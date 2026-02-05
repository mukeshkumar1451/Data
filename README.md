import re


def parse_llm_steps(llm_text: str):
    """
    Parse LLM output and MERGE ALL steps into ONE SINGLE testcase.
    Step numbers will be re-sequenced continuously.

    Returns:
    [
        {
            "scenario": str,
            "script": str,
            "precondition": str,
            "requirement": str,
            "steps": [
                {
                    "step_no": "Step 01",
                    "action": "...",
                    "screen": "...",
                    "testdata": "...",
                    "expected": "..."
                }
            ]
        }
    ]
    """

    lines = llm_text.split("\n")

    scenario = ""
    script = ""
    precondition = ""
    requirement = ""

    steps = []
    step_counter = 1

    for raw in lines:
        line = raw.strip()

        if line.startswith("Scenario:"):
            scenario = line.split(":", 1)[1].strip()

        elif line.startswith("Script:"):
            script = line.split(":", 1)[1].strip()

        elif line.startswith("Precondition:"):
            precondition = line.split(":", 1)[1].strip()

        elif line.startswith("Requirement:"):
            requirement = line.split(":", 1)[1].strip()

        elif re.match(r"^Step\s*\d+", line):
            parts = [p.strip() for p in line.split("|")]

            if len(parts) >= 5:
                steps.append({
                    "step_no": f"Step {step_counter:02d}",
                    "action": parts[1],
                    "screen": parts[2],
                    "testdata": parts[3],
                    "expected": parts[4],
                })
                step_counter += 1

    # 🔥 Always return ONLY ONE testcase
    return [{
        "scenario": scenario,
        "script": script,
        "precondition": precondition,
        "requirement": requirement,
        "steps": steps
    }]
