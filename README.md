def _parse_llm_output(self, llm_text: str) -> Dict:
        scenario = ""
        script = ""
        requirement = ""
        steps = []
        step_counter = 1

        for raw in llm_text.splitlines():
            line = raw.strip()

            if not line:
                continue

        # ---------------- headers ----------------
            if line.lower().startswith("scenario:") and not scenario:
                scenario = line.split(":", 1)[1].strip()
                continue

            if line.lower().startswith("script:") and not script:
                script = line.split(":", 1)[1].strip()
                continue

            if line.lower().startswith("requirement:") and not requirement:
                requirement = line.split(":", 1)[1].strip()
                continue

        # ---------------- steps ----------------
            if re.match(r"^step\s*\d+", line.lower()):

            # remove "Step 01"
                cleaned = re.sub(r"^step\s*\d+\s*", "", line, flags=re.IGNORECASE).strip()

            # split by pipe if exists
                if "|" in cleaned:
                    parts = [p.strip() for p in cleaned.split("|")]
                else:
                # legacy format -> treat entire line as description
                    parts = [cleaned]

            # normalize to 4 fields
                while len(parts) < 4:
                    parts.append("")

                steps.append({
                    "step_no": f"Step {step_counter:02d}",
                    "desc": parts[0],
                    "screen": parts[1],
                    "data": parts[2],
                    "expected": parts[3],
                })
 
                step_counter += 1

        return {
            "scenario": scenario,
            "script": script,
            "requirement": requirement,
            "steps": steps
    }

