import os
import logging
from openpyxl import load_workbook
from config.config import get

logger = logging.getLogger(__name__)


class ExcelExportAgent:

    def __init__(self):
        self.template_path = get("EXCEL_TEMPLATE_PATH")
        self.output_dir = get("EXCEL_OUTPUT_DIR")

    # -------------------------------------------------
    # SAFE PIPE FORMAT PARSER
    # -------------------------------------------------
    def _parse_llm_output(self, llm_text: str):

        scenario = ""
        script = ""
        requirement = ""
        steps = []

        lines = llm_text.splitlines()

        for line in lines:
            line = line.strip()

            if not line:
                continue

            if line.startswith("Test Scenario Description:") or line.startswith("Scenario:"):
                scenario = (
                    line.replace("Test Scenario Description:", "")
                        .replace("Scenario:", "")
                        .strip()
                )
                continue

            if line.startswith("Test Script Description:") or line.startswith("Script:"):
                script = (
                    line.replace("Test Script Description:", "")
                        .replace("Script:", "")
                        .strip()
                )
                continue

            if line.startswith("Test Scenario Id:"):
                requirement = line.replace("Test Scenario Id:", "").strip()
                continue

            if line.lower().startswith("step") and "|" in line:

                parts = [p.strip() for p in line.split("|")]

                if len(parts) < 6:
                    logger.warning(f"Skipping malformed step line: {line}")
                    continue

                if len(parts) > 6:
                    parts = parts[:5] + [" | ".join(parts[5:])]

                steps.append({
                    "step_no": parts[0],
                    "desc": parts[1],
                    "screen": parts[2],
                    "data": parts[3],
                    "expected": parts[4],
                    "requirement": parts[5]
                })

        return {
            "scenario": scenario,
            "script": script,
            "requirement": requirement,
            "steps": steps
        }

    # -------------------------------------------------
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

        logger.info(f"Sheets after cleanup: {wb.sheetnames}")

        user_story_id = state["user_story_id"]

        for channel, llm_text in state.get("llm_outputs", {}).items():

            if channel not in wb.sheetnames:
                logger.warning(f"Sheet '{channel}' not found after cleanup.")
                continue

            # ...removed log file saving...

            ws = wb[channel]
            row = 2
            tc_counter = 1

            parsed = self._parse_llm_output(llm_text)

            if not parsed["steps"]:
                logger.error(f"No valid steps parsed for channel {channel}")
                continue

            scenario = parsed["scenario"] or f"Validate {state.get('user_story', '')}"
            script = parsed["script"] or "Positive validation aligned to Acceptance Criteria."

            channel_ctx = state.get("channel_context", {})
            precondition = channel_ctx.get(channel, {}).get("precondition", "")

            tc_id = f"US_{user_story_id}_{channel}_TC_{tc_counter:02d}"
            start_row = row

            for idx, step in enumerate(parsed["steps"]):

                ws.cell(row, 1).value = tc_id if idx == 0 else ""
                ws.cell(row, 2).value = f"{user_story_id}-{channel}" if idx == 0 else ""
                ws.cell(row, 3).value = scenario if idx == 0 else ""
                ws.cell(row, 4).value = script if idx == 0 else ""
                ws.cell(row, 5).value = precondition if idx == 0 else ""

                ws.cell(row, 6).value = step["step_no"]
                ws.cell(row, 7).value = step["desc"]
                ws.cell(row, 8).value = step["screen"]
                ws.cell(row, 9).value = step["data"]
                ws.cell(row, 10).value = step["expected"]

                ws.cell(row, 11).value = ""
                ws.cell(row, 12).value = ""
                ws.cell(row, 13).value = ""
                ws.cell(row, 14).value = ""
                ws.cell(row, 15).value = step["requirement"]

                row += 1

            end_row = row - 1

            if end_row > start_row:
                ws.merge_cells(
                    start_row=start_row,
                    start_column=2,
                    end_row=end_row,
                    end_column=2
                )

            tc_counter += 1

        # -------------------------------------------------
        # OUTPUT FILE WITH AUTO VERSIONING
        # -------------------------------------------------
        base_filename = f"Indiv_US_{user_story_id}_Test_Scripts_v1.0.xlsx"
        output_path = os.path.join(self.output_dir, base_filename)
        wb.save(output_path)
        state["excel_output"] = output_path
        return state
