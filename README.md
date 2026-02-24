import logging
import os
from typing import Dict

from openpyxl import load_workbook
from config.config import get

logger = logging.getLogger(__name__)


class ExcelExportAgent:

    def __init__(self):
        self.template_path = get("EXCEL_TEMPLATE_PATH")
        self.output_dir = get("EXCEL_OUTPUT_DIR")

    # ---------------------------------------------------------
    # Extract precondition (everything before "Scenario:")
    # ---------------------------------------------------------
    def _extract_precondition(self, text: str) -> str:

        if "Scenario:" in text:
            return text.split("Scenario:")[0].strip()

        return ""

    # ---------------------------------------------------------
    # Extract scenario / script / requirement
    # ---------------------------------------------------------
    def _extract_header(self, text: str) -> Dict:

        scenario = ""
        script = ""
        requirement = ""

        for line in text.splitlines():
            line = line.strip()

            if line.startswith("Scenario:"):
                scenario = line.replace("Scenario:", "").strip()

            elif line.startswith("Script:"):
                script = line.replace("Script:", "").strip()

            elif line.startswith("Requirement:"):
                requirement = line.replace("Requirement:", "").strip()

        return {
            "scenario": scenario,
            "script": script,
            "requirement": requirement
        }

    # ---------------------------------------------------------
    # Extract step lines (pipe-based)
    # ---------------------------------------------------------
    def _extract_steps(self, text: str):

        steps = []

        for line in text.splitlines():

            line = line.strip()

            if line.startswith("Step") and "|" in line:

                parts = [p.strip() for p in line.split("|")]

                if len(parts) == 5:
                    steps.append({
                        "step_no": parts[0],
                        "desc": parts[1],
                        "screen": parts[2],
                        "data": parts[3],
                        "expected": parts[4],
                    })
                else:
                    logger.warning(f"Invalid step format: {line}")

        return steps

    # ---------------------------------------------------------
    # Write test case into Excel
    # ---------------------------------------------------------
    def _write_testcase(self, ws, start_row, tc_id, header, precondition, steps):

        row = start_row

        for idx, step in enumerate(steps):

            ws.cell(row, 1).value = tc_id if idx == 0 else ""
            ws.cell(row, 2).value = header["script"] if idx == 0 else ""
            ws.cell(row, 3).value = "NA" if idx == 0 else ""
            ws.cell(row, 4).value = header["scenario"] if idx == 0 else ""
            ws.cell(row, 5).value = precondition if idx == 0 else ""

            ws.cell(row, 6).value = step["step_no"]
            ws.cell(row, 7).value = step["desc"]
            ws.cell(row, 8).value = step["screen"]
            ws.cell(row, 9).value = step["data"]
            ws.cell(row, 10).value = step["expected"]

            ws.cell(row, 11).value = header["requirement"] if idx == 0 else ""

            row += 1

        return row

    # ---------------------------------------------------------
    # LangGraph Entry
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("Excel Export Agent started")

        os.makedirs(self.output_dir, exist_ok=True)
        wb = load_workbook(self.template_path)

        story_channels = state.get("channels", [])
        llm_outputs = state.get("llm_outputs", {})

        # Remove unused sheets
        for sheet in list(wb.sheetnames):
            if sheet not in story_channels:
                wb.remove(wb[sheet])

        # Create missing sheets
        for ch in story_channels:
            if ch not in wb.sheetnames:
                wb.create_sheet(ch)

        sheets = {name: wb[name] for name in wb.sheetnames}
        row_tracker = {ch: 2 for ch in story_channels}
        tc_counter = {ch: 1 for ch in story_channels}

        user_story_id = state["user_story_id"]

        for channel in story_channels:

            llm_text = llm_outputs.get(channel)

            if not llm_text:
                logger.warning(f"No LLM output for {channel}")
                continue

            precondition = self._extract_precondition(llm_text)
            header = self._extract_header(llm_text)
            steps = self._extract_steps(llm_text)

            logger.info(f"{channel} -> Parsed {len(steps)} steps")

            ws = sheets[channel]

            tc_id = f"US_{user_story_id}_TC_{tc_counter[channel]:02d}"

            new_row = self._write_testcase(
                ws,
                row_tracker[channel],
                tc_id,
                header,
                precondition,
                steps
            )

            row_tracker[channel] = new_row
            tc_counter[channel] += 1

        output_file = os.path.join(
            self.output_dir,
            f"Indiv_US_{user_story_id}_Test Scripts_v1.0.xlsx"
        )

        wb.save(output_file)

        logger.info(f"Excel generated: {output_file}")

        state["excel_output"] = output_file
        return state
