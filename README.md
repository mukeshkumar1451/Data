import re


def parse_llm_steps(llm_text: str, channels: list):
    """
    Convert LLM raw text into structured testcases for Excel exporter
    """

    testcases = []

    blocks = llm_text.split("Scenario:")
    for block in blocks:
        block = block.strip()
        if not block:
            continue

        lines = [l.strip() for l in block.split("\n") if l.strip()]

        scenario = ""
        script = ""
        precondition = ""
        requirement = ""
        steps = []

        for line in lines:

            if line.startswith("Script:"):
                script = line.split(":", 1)[1].strip()

            elif line.startswith("Precondition:"):
                precondition = line.split(":", 1)[1].strip()

            elif line.startswith("Requirement:"):
                requirement = line.split(":", 1)[1].strip()

            elif line.startswith("Step"):
                parts = [p.strip() for p in line.split("|")]

                if len(parts) >= 5:
                    steps.append({
                        "step_no": parts[0],
                        "desc": parts[1],      # ✅ EXACT KEY Excel expects
                        "screen": parts[2],
                        "data": parts[3],
                        "expected": parts[4],
                    })

            else:
                # first line after Scenario:
                if not scenario:
                    scenario = line.strip()

        testcases.append({
            "scenario": scenario,
            "script": script,
            "precondition": precondition,
            "requirement": requirement,
            "steps": steps,
            "channels": channels  # attach here directly
        })

    return testcases
