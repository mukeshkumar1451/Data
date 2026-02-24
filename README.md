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
    # CLEAN MARKDOWN / FORMAT ARTIFACTS
    # ---------------------------------------------------------
    def _clean_text(self, text: str) -> str:
        if not text:
            return ""

        text = text.replace("**", "")
        text = text.replace("####", "")
        text = text.replace("---", "")
        return text.strip()

    # ---------------------------------------------------------
    # EXTRACT PRECONDITION (STOP EXACTLY AFTER LOAN STAGE)
    # ---------------------------------------------------------
    def _extract_precondition(self, llm_text: str) -> str:

        llm_text = self._clean_text(llm_text)
        lines = llm_text.splitlines()

        capture = False
        block = []

        for line in lines:
            clean_line = line.strip()

            if "Create a loan from" in clean_line:
                capture = True

            if capture:
                block.append(clean_line)

                # Stop after Loan Stage line
                if clean_line.lower().startswith("5.") and "loan stage" in clean_line.lower():
                    break

        return "\n".join(block).strip()

    # ---------------------------------------------------------
    # PARSE STEPS (ROBUST MULTI-CHANNEL SAFE)
    # ---------------------------------------------------------
    def _parse_llm_output(self, llm_text: str) -> Dict:

        llm_text = self._clean_text(llm_text)

        steps = []
        step_counter = 1
        current_step = None

        lines = llm_text.splitlines()

        for line in lines:

            clean = line.strip()

            if not clean:
                continue

            # Remove bullet prefix if exists
            clean = clean.lstrip("- ").strip()

            # Detect Step Header
            if re.search(r"\bstep\s*\d+\b", clean.lower()):

                if current_step:
                    steps.append(current_step)

                current_step = {
                    "step_no": f"Step {step_counter:02d}",
                    "desc": "",
                    "screen": "",
                    "data": "",
                    "expected": ""
                }

                step_counter += 1
                continue

            if current_step:

                lower = clean.lower()

                # Description
                if lower.startswith("description"):
                    parts = clean.split(":", 1)
                    if len(parts) > 1:
                        current_step["desc"] = parts[1].strip()

                # Screen
                elif lower.startswith("screen"):
                    parts = clean.split(":", 1)
                    if len(parts) > 1:
                        current_step["screen"] = parts[1].strip()

                # Test Data
                elif lower.startswith("test data"):
                    parts = clean.split(":", 1)
                    if len(parts) > 1:
                        current_step["data"] = parts[1].strip()

                # Expected Result / Expected Results
                elif lower.startswith("expected"):
                    parts = clean.split(":", 1)
                    if len(parts) > 1:
                        current_step["expected"] = parts[1].strip()

        if current_step:
            steps.append(current_step)

        return {
            "scenario": "Generated Scenario",
            "script": "Generated Script",
            "requirement": "Derived from User Story",
            "steps": steps
        }

    # ---------------------------------------------------------
    # WRITE TO EXCEL (MATCHES YOUR TEMPLATE)
    # ---------------------------------------------------------
    def _write_testcase(self, ws, start_row, tc_id, tc_data, precondition):

        row = start_row

        for idx, step in enumerate(tc_data["steps"]):

            if idx == 0:
                ws.cell(row, 1).value = tc_id
                ws.cell(row, 2).value = f"{tc_id}_SC_01"
                ws.cell(row, 3).value = tc_data["scenario"]
                ws.cell(row, 4).value = tc_data["script"]
                ws.cell(row, 5).value = precondition
                ws.cell(row, 15).value = tc_data["requirement"]

            ws.cell(row, 6).value = step["step_no"]
            ws.cell(row, 7).value = step["desc"]
            ws.cell(row, 8).value = step["screen"]
            ws.cell(row, 9).value = step["data"]
            ws.cell(row, 10).value = step["expected"]

            # Execution columns left blank intentionally
            ws.cell(row, 11).value = ""
            ws.cell(row, 12).value = ""
            ws.cell(row, 13).value = ""
            ws.cell(row, 14).value = ""

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
                logger.warning(f"No LLM output for {channel}")
                continue

            ws = sheets[channel]

            tc_data = self._parse_llm_output(llm_text)
            precondition = self._extract_precondition(llm_text)

            logger.info(f"{channel} -> Parsed {len(tc_data['steps'])} steps")

            tc_id = f"US_{user_story_id}_TC_{tc_counter[channel]:02d}"
            row = row_tracker[channel]

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
