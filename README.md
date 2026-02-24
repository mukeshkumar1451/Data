def _extract_precondition(self, llm_text: str) -> str:

    # Remove markdown
    llm_text = llm_text.replace("**", "")
    llm_text = llm_text.replace("####", "")
    llm_text = llm_text.replace("---", "")

    lines = llm_text.splitlines()

    capture = False
    block = []

    for line in lines:
        clean_line = line.strip()

        # Start capturing
        if "Create a loan from" in clean_line:
            capture = True

        if capture:
            block.append(clean_line)

            # Stop exactly after Loan Stage line
            if clean_line.lower().startswith("5.") and "loan stage" in clean_line.lower():
                break

    return "\n".join(block).strip()
