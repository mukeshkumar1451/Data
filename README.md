import re


def parse_llm_steps(llm_text, channels):
    """
    Converts LLM raw text into structured testcases
    """

    testcases = []

    # Split multiple testcases if present
    blocks = llm_text.split("Scenario:")

    for block in blocks:
        block = block.strip()
        if not block:
            continue

        lines = block.split("\n")

        scenario = ""
        script = ""
        precondition = ""
        requirement = ""

        steps = []

        for line in lines:
            line = line.strip()

            if line.startswith("Scenario:"):
                scenario = line.replace("Scenario:", "").strip()

            elif line.startswith("Script:"):
                script = line.replace("Script:", "").strip()

            elif line.startswith("Precondition:"):
                precondition = line.replace("Precondition:", "").strip()

            elif line.startswith("Requirement:"):
                requirement = line.replace("Requirement:", "").strip()

            elif line.startswith("Step"):
                parts = [p.strip() for p in line.split("|")]

                if len(parts) >= 5:
                    steps.append({
                        "step_no": parts[0],
                        "action": parts[1],
                        "screen": parts[2],
                        "testdata": parts[3],
                        "expected": parts[4],
                    })

        testcases.append({
            "scenario": scenario,
            "script": script,
            "precondition": precondition,
            "requirement": requirement,
            "channels": channels,
            "steps": steps
        })

    return testcases
