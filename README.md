import re


def parse_llm_steps(llm_text, channels):
    """
    Convert LLM raw text into structured testcase objects
    """

    testcases = []
    current_tc = None

    lines = [l.strip() for l in llm_text.splitlines() if l.strip()]

    step_pattern = re.compile(r"^Step\s+\d+", re.IGNORECASE)

    for line in lines:

        # ---------------------------------------
        # Start of new testcase
        # ---------------------------------------
        if line.startswith("Scenario:"):
            if current_tc:
                testcases.append(current_tc)

            current_tc = {
                "scenario": line.split(":", 1)[1].strip(),
                "script": "",
                "precondition": "",
                "requirement": "",
                "steps": [],
                "channels": channels
            }

        elif line.startswith("Script:"):
            current_tc["script"] = line.split(":", 1)[1].strip()

        elif line.startswith("Precondition:"):
            current_tc["precondition"] = line.split(":", 1)[1].strip()

        elif line.startswith("Requirement:"):
            current_tc["requirement"] = line.split(":", 1)[1].strip()

        # ---------------------------------------
        # Step lines
        # ---------------------------------------
        elif step_pattern.match(line):
            parts = [p.strip() for p in line.split("|")]

            if len(parts) >= 5:
                step = {
                    "step_no": parts[0],
                    "desc": parts[1],
                    "screen": parts[2],
                    "data": parts[3],
                    "expected": parts[4]
                }
                current_tc["steps"].append(step)

    # append last testcase
    if current_tc:
        testcases.append(current_tc)

    print(f"✅ Parsed {len(testcases)} testcases from LLM output")

    return testcases
