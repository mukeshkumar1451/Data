def _extract_precondition(self, content: str) -> str:

    lines = content.splitlines()
    capture = False
    collected = []

    for line in lines:

        lower = line.lower()

        # Detect precondition header variations
        if (
            "pre-condition" in lower or
            "precondition" in lower or
            "pre condition" in lower
        ):
            capture = True
            collected.append(line)
            continue

        # Stop when steps begin
        if capture and line.strip().lower().startswith("step"):
            break

        if capture:
            collected.append(line)

    return "\n".join(collected).strip()
