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
    # CLEAN MARKDOWN
    # ---------------------------------------------------------
    def _clean_text(self, text: str) -> str:
        if not text:
            return ""
        text = text.replace("**", "")
        text = text.replace("####", "")
        text = text.replace("---", "")
        return text.strip()

    # ---------------------------------------------------------
    # EXTRACT PRECONDITION (ROBUST)
    # ---------------------------------------------------------
    def _extract_precondition(self, llm_text: str) -> str:

        llm_text = self._clean_text(llm_text)

        match = re.search(
            r"(Create a loan from .*?Loan Stage:\s*.*)",
            llm_text,
            re.DOTALL | re.IGNORECASE
        )

        if not match:
            logger.warning("Precondition not detected.")
            return ""

        block = match.group(1)

        # Stop before Step 1 if included
        block = re.split(r"Step\s*1", block, flags=re.IGNORECASE)[0]

        return block.strip()

    # ---------------------------------------------------------
    # PARSE LLM OUTPUT INTO STRUCTURED STEPS
    # ---------------------------------------------------------
    def _parse_llm_output(self, llm_text: str) -> Dict:

        llm_text = self._clean_text(llm_text)

        steps = []
        step_counter = 1
        current_step = None

        for raw in llm_text.splitlines():
            line = raw.strip()

            if not line:
                continue

            # Detect step header
            if re.search(r"step\s*\d+", line.lower()):
                if current_step:
                    steps.append(current_step)

                current_step = {
                    "step_no": f"Step {step_counter:02d}",
                    "desc": "",
                    "screen": "NA",
                    "data": "NA",
                    "expected": "Verify system behavior"
                }

                step_counter += 1
                continue

            if current_step:

                if "description" in line.lower():
                    current_step["desc"] = line.split(":", 1)[-1].strip()

                elif "screen" in line.lower():
                    current_step["screen"] = line.split(":", 1)[-1].strip()

                elif "test data" in line.lower():
                    current_step["data"] = line.split(":", 1)[-1].strip()

                elif "expected" in line.lower():
                    current_step["expected"] = line.split(":", 1)[-1].strip()

        if current_step:
            steps.append(current_step)

        return {
            "scenario": "Generated Scenario",
            "script": "Generated Script",
            "requirement": "Derived from User Story",
            "steps": steps
        }

    # ---------------------------------------------------------
    # WRITE TESTCASE TO SHEET
    # ---------------------------------------------------------
    def _write_testcase(self, ws, start_row, tc_id, tc_data, precondition):

        row = start_row

        for idx, step in enumerate(tc_data["steps"]):

            ws.cell(row, 1).value = tc_id if idx == 0 else ""
            ws.cell(row, 2).value = tc_data["script"] if idx == 0 else ""
            ws.cell(row, 3).value = ""
            ws.cell(row, 4).value = tc_data["scenario"] if idx == 0 else ""
            ws.cell(row, 5).value = precondition if idx == 0 else ""

            ws.cell(row, 6).value = step["step_no"]
            ws.cell(row, 7).value = step["desc"]
            ws.cell(row, 8).value = step["screen"]
            ws.cell(row, 9).value = step["data"]
            ws.cell(row, 10).value = step["expected"]

            ws.cell(row, 11).value = tc_data["requirement"] if idx == 0 else ""

            row += 1

        return row

    # ---------------------------------------------------------
    # LANGGRAPH ENTRY
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

        # Ensure required sheets exist
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

            precondition = self._extract_precondition(llm_text)

            if not precondition:
                logger.warning(f"No precondition extracted for {channel}")

            row = row_tracker[channel]
            tc_id = f"US_{user_story_id}_TC_{tc_counter[channel]:02d}"

            logger.info(f"Writing {channel} with {len(tc_data['steps'])} steps")

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
