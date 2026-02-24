import os
import logging
from openpyxl import load_workbook
from config.config import get

logger = logging.getLogger(__name__)


class ExcelExportAgent:

    def __init__(self):
        self.template_path = get("EXCEL_TEMPLATE_PATH")
        self.output_dir = get("EXCEL_OUTPUT_DIR")

    # ---------------------------------------------------
    # STRICT PIPE PARSER (NO REGEX)
    # ---------------------------------------------------
    def _parse_llm_output(self, llm_text: str):

        scenario = ""
        script = ""
        requirement = ""
        precondition = ""
        steps = []

        lines = llm_text.splitlines()
        step_counter = 1

        for line in lines:
            line = line.strip()

            if not line:
                continue

            if line.startswith("Scenario:"):
                scenario = line.replace("Scenario:", "").strip()
                continue

            if line.startswith("Script:"):
                script = line.replace("Script:", "").strip()
                continue

            if line.startswith("Requirement:"):
                requirement = line.replace("Requirement:", "").strip()
                continue

            if line.startswith("PRECONDITION:"):
                continue

            # Capture precondition lines (numbered)
            if line.startswith("1.") or line.startswith("2.") or \
               line.startswith("3.") or line.startswith("4.") or \
               line.startswith("5.") or line.startswith("Create"):
                precondition += line + "\n"
                continue

            # Parse only pipe formatted steps
            if "|" in line and line.lower().startswith("step"):
                parts = [p.strip() for p in line.split("|")]

                if len(parts) != 5:
                    continue

                steps.append({
                    "step_no": f"Step {step_counter:02d}",
                    "desc": parts[1],
                    "screen": parts[2],
                    "data": parts[3],
                    "expected": parts[4]
                })

                step_counter += 1

        return {
            "scenario": scenario,
            "script": script,
            "requirement": requirement,
            "precondition": precondition.strip(),
            "steps": steps
        }

    # ---------------------------------------------------
    # MAIN RUN
    # ---------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("Excel Export Agent started")

        os.makedirs(self.output_dir, exist_ok=True)

        wb = load_workbook(self.template_path)
        ws = wb.active  # single sheet template

        row = 2  # Start writing from row 2
        tc_counter = 1
        user_story_id = state["user_story_id"]

        for channel, llm_text in state["llm_outputs"].items():

            parsed = self._parse_llm_output(llm_text)

            logger.info(f"{channel} -> Parsed {len(parsed['steps'])} steps")

            if not parsed["steps"]:
                continue

            tc_id = f"US_{user_story_id}_{channel}_TC_{tc_counter:02d}"

            for idx, step in enumerate(parsed["steps"]):

                ws.cell(row, 1).value = tc_id if idx == 0 else ""
                ws.cell(row, 2).value = f"{user_story_id}-{channel}"
                ws.cell(row, 3).value = parsed["scenario"] if idx == 0 else ""
                ws.cell(row, 4).value = parsed["script"] if idx == 0 else ""
                ws.cell(row, 5).value = parsed["precondition"] if idx == 0 else ""

                ws.cell(row, 6).value = step["step_no"]
                ws.cell(row, 7).value = step["desc"]
                ws.cell(row, 8).value = step["screen"]
                ws.cell(row, 9).value = step["data"]
                ws.cell(row, 10).value = step["expected"]

                ws.cell(row, 11).value = ""  # Actual Results
                ws.cell(row, 12).value = ""  # Status
                ws.cell(row, 13).value = ""  # Comments
                ws.cell(row, 14).value = ""  # Post Condition
                ws.cell(row, 15).value = parsed["requirement"] if idx == 0 else ""

                row += 1

            tc_counter += 1

        output_file = os.path.join(
            self.output_dir,
            f"Indiv_US_{user_story_id}_Test_Scripts_v1.0.xlsx"
        )

        wb.save(output_file)

        logger.info(f"Excel generated: {output_file}")

        state["excel_output"] = output_file
        return state
