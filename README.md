import re

def parse_llm_steps(llm_text: str):
    """
    Converts LLM raw testcase text into structured step rows
    """

    steps = []

    lines = llm_text.split("\n")

    step_pattern = re.compile(r"Step\s*\d+", re.IGNORECASE)

    for line in lines:
        line = line.strip()

        if not line:
            continue

        if step_pattern.match(line):
            # Expected format:
            # Step 01 | Action | Screen | TestData | Expected
            parts = [p.strip() for p in line.split("|")]

            if len(parts) >= 5:
                steps.append({
                    "step_no": parts[0],
                    "action": parts[1],
                    "screen": parts[2],
                    "testdata": parts[3],
                    "expected": parts[4]
                })

    return steps
