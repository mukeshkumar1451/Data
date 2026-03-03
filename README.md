import os
import logging
import re
from openpyxl import load_workbook
from config.config import get

logger = logging.getLogger(__name__)


class ExcelExportAgent:

    def __init__(self):
        self.template_path = get("EXCEL_TEMPLATE_PATH")
        self.output_dir = get("EXCEL_OUTPUT_DIR")

    # -------------------------------------------------
    # MARKDOWN STEP PARSER
    # -------------------------------------------------
    def _parse_markdown_steps(self, llm_text: str):

        scenario = ""
        script = ""
        steps = []

        lines = llm_text.splitlines()

        current_step = None

        for line in lines:
            line = line.strip()

            # Scenario
            if "Test Scenario Description" in line:
                scenario = line.split(":", 1)[-1].strip()

            if "Test Script Description" in line:
                script = line.split(":", 1)[-1].strip()

            # Detect Step
            step_match = re.match(r".*Step\s+(\d+)", line, re.IGNORECASE)

            if step_match:
                if current_step:
                    steps.append(current_step)

                current_step = {
                    "step_no": f"Step {int(step_match.group(1)):02d}",
                    "desc": "",
                    "expected": ""
                }
                continue

            # Capture Description
            if line.lower().startswith("- description"):
                current_step["desc"] = line.split(":", 1)[-1].strip()

            # Capture Expected
            if line.lower().startswith("- expected"):
                current_step["expected"] = line.split(":", 1)[-1].strip()

        if current_step:
            steps.append(current_step)

        return {
            "scenario": scenario,
            "script": script,
            "steps": steps
        }

    # -------------------------------------------------
    # MAIN EXECUTION
    # -------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("Excel Export Agent started")

        os.makedirs(self.output_dir, exist_ok=True)

        wb = load_workbook(self.template_path)

        detected_channels = state.get("channels", [])

        # Remove unused sheets
        if detected_channels:
            for sheet_name in list(wb.sheetnames):
                if sheet_name not in detected_channels:
                    wb.remove(wb[sheet_name])

        user_story_id = state["user_story_id"]

        for channel, llm_text in state.get("llm_outputs", {}).items():

            if channel not in wb.sheetnames:
                continue

            ws = wb[channel]
            row = 2

            parsed = self._parse_markdown_steps(llm_text)

            if not parsed["steps"]:
                logger.error(f"No steps parsed for {channel}")
                continue

            scenario = parsed["scenario"] or state.get("user_story", "")
            script = parsed["script"] or "Generated test validation."

            tc_id = f"US_{user_story_id}_{channel}_TC_01"

            for idx, step in enumerate(parsed["steps"]):

                ws.cell(row, 1).value = tc_id if idx == 0 else ""
                ws.cell(row, 2).value = f"{user_story_id}-{channel}" if idx == 0 else ""
                ws.cell(row, 3).value = scenario if idx == 0 else ""
                ws.cell(row, 4).value = script if idx == 0 else ""
                ws.cell(row, 5).value = ""

                ws.cell(row, 6).value = step["step_no"]
                ws.cell(row, 7).value = step["desc"]
                ws.cell(row, 8).value = "Derived Screen"
                ws.cell(row, 9).value = "NA"
                ws.cell(row, 10).value = step["expected"]

                ws.cell(row, 15).value = f"{user_story_id}_AC"

                row += 1

        base_filename = f"Indiv_US_{user_story_id}_Test_Scripts.xlsx"
        output_file = os.path.join(self.output_dir, base_filename)

        wb.save(output_file)

        logger.info(f"Excel generated: {output_file}")

        state["excel_output"] = output_file
        return state
