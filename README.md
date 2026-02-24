import logging
import os
import re
from typing import Dict

from openpyxl import load_workbook
from config.config import get

logger = logging.getLogger(__name__)


class ExcelExportAgent:

    def __init__(self):
        self.template_path = get("EXCEL_TEMPLATE_PATH")
        self.output_dir = get("EXCEL_OUTPUT_DIR")

    # ---------------------------------------------------------
    # Extract Precondition directly from LLM Output
    # ---------------------------------------------------------
    def _extract_precondition_from_llm(self, text: str) -> str:

        if "Create a loan from" not in text:
            logger.warning("Precondition block not found in LLM output")
            return ""

        block = text.split("Create a loan from", 1)[1]
        block = "Create a loan from" + block

        # Stop before first Step
        step_match = re.search(r"\n\s*Step\s*\d+", block, re.IGNORECASE)
        if step_match:
            block = block[:step_match.start()]

        return block.strip()

    # ---------------------------------------------------------
    # Parse Steps from LLM output
    # ---------------------------------------------------------
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

            if re.match(r"^step\s*\d+", line.lower()):

                cleaned = re.sub(r"^step\s*\d+\s*[:\-]?\s*", "", line, flags=re.IGNORECASE)

                desc = cleaned
                screen = "NA"
                data = "NA"
                expected = "Verify system behavior"

                steps.append({
                    "step_no": f"Step {step_counter:02d}",
                    "desc": desc,
                    "screen": screen,
                    "data": data,
                    "expected": expected,
                })

                step_counter += 1

        return {
            "scenario": scenario,
            "script": script,
            "requirement": requirement,
            "steps": steps
        }

    # ---------------------------------------------------------
    def _write_testcase(self, ws, start_row, tc_id, tc_data, precondition):

        row = start_row

        for idx, step in enumerate(tc_data["steps"]):

            ws.cell(row, 1).value = tc_id if idx == 0 else ""
            ws.cell(row, 2).value = "Generated Script" if idx == 0 else ""
            ws.cell(row, 3).value = "NA" if idx == 0 else ""
            ws.cell(row, 4).value = "Generated Scenario" if idx == 0 else ""
            ws.cell(row, 5).value = precondition if idx == 0 else ""

            ws.cell(row, 6).value = step["step_no"]
            ws.cell(row, 7).value = step["desc"]
            ws.cell(row, 8).value = step["screen"]
            ws.cell(row, 9).value = step["data"]
            ws.cell(row, 10).value = step["expected"]

            ws.cell(row, 11).value = "Derived from User Story" if idx == 0 else ""

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

        logger.info(f"Detected channels: {story_channels}")

        # Remove unused sheets
        for sheet in list(wb.sheetnames):
            if sheet not in story_channels:
                wb.remove(wb[sheet])

        # Ensure sheets exist
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
                logger.warning(f"No LLM output for {channel}, skipping")
                continue

            ws = sheets[channel]

            tc_data = self._parse_llm_output(llm_text)
            precondition = self._extract_precondition_from_llm(llm_text)

            logger.info(f"\nExtracted Precondition for {channel}:\n{precondition}\n")

            row = row_tracker[channel]
            tc_id = f"US_{user_story_id}_TC_{tc_counter[channel]:02d}"

            new_row = self._write_testcase(ws, row, tc_id, tc_data, precondition)

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
