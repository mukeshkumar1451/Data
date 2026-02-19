
import logging
import os
import re
from typing import Dict

from openpyxl import load_workbook
from config.config import get
from utils.product_mapper import resolve_product_code
from utils.loan_domain_normalizer import normalize_full_setup

logger = logging.getLogger(__name__)


class ExcelExportAgent:

    def __init__(self):
        self.template_path = get("EXCEL_TEMPLATE_PATH")
        self.output_dir = get("EXCEL_OUTPUT_DIR")

    # ---------------------------------------------------------
    # Parse raw LLM text into structured testcase
    # ---------------------------------------------------------
    def _parse_llm_output(self, llm_text: str) -> Dict:
        scenario = ""
        script = ""
        requirement = ""
        steps = []
        step_counter = 1

        GENERIC_WORDS = ["action", "verify", "check", "navigate", "enter", "select"]

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

                # split pipe or legacy format
                if "|" in cleaned:
                    parts = [p.strip() for p in cleaned.split("|")]
                else:
                    parts = [cleaned]

                # remove empties
                parts = [p for p in parts if p]

                if not parts:
                    continue

                # ---------------- intelligent column mapping ----------------
                if len(parts) >= 4:

                    first = parts[0].lower()

                    # LLM inserted verb column → shift left
                    if first in GENERIC_WORDS:
                        desc = parts[1]
                        screen = parts[2] if len(parts) > 2 else "NA"
                        data = parts[3] if len(parts) > 3 else "NA"
                        expected = parts[4] if len(parts) > 4 else "Verify system behavior"
                    else:
                        desc = parts[0]
                        screen = parts[1] if len(parts) > 1 else "NA"
                        data = parts[2] if len(parts) > 2 else "NA"
                        expected = parts[3] if len(parts) > 3 else "Verify system behavior"

                elif len(parts) == 3:
                    desc, screen, data = parts
                    expected = "Verify system behavior"

                elif len(parts) == 2:
                    desc, screen = parts
                    data = "NA"
                    expected = "Verify system behavior"

                else:
                    desc = parts[0]
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
    # Convert inferred setup -> Template precondition
    # ---------------------------------------------------------
    def _format_precondition(self, channel: str, setup_text: str) -> str:

        if not setup_text:
            logger.warning(f"⚠️ No setup found for {channel}, using fallback")
            return f"Channel: {channel}"

        # 🔥 USE NORMALIZED STRUCTURE
        data = normalize_full_setup(channel, setup_text)

        loan_purpose = data["loan_purpose"]
        loan_type = data["loan_type"]
        loan_stage = data["loan_stage"]

        product_code = resolve_product_code(
            loan_type=loan_type,
            channel=channel
        )

        portal_map = {
            "RTL": "Customer Portal",
            "WHL": "Broker Portal",
            "CL1": "Broker Portal",
            "DTC": "Ignite Portal"
        }

        portal = portal_map.get(channel, "Portal")

        formatted = f"""Create a loan from {portal} as per pre-conditions below:
1. Channel: {channel}
2. Loan Purpose: {loan_purpose}
3. Loan Type: {loan_type}
4. Product Code: {product_code}
5. Loan Stage: {loan_stage}"""

        return formatted

    # ---------------------------------------------------------
    def _write_testcase(self, ws, start_row, tc_id, tc_data, precondition):
        row = start_row

        for idx, step in enumerate(tc_data["steps"]):
            ws.cell(row, 1).value = tc_id if idx == 0 else ""
            ws.cell(row, 2).value = tc_data["script"] if idx == 0 else ""
            ws.cell(row, 3).value = "NA" if idx == 0 else ""
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
    # LangGraph entry
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:
        logger.info("📄 Excel Export Agent started")

        os.makedirs(self.output_dir, exist_ok=True)
        wb = load_workbook(self.template_path)

        for ch in ["RTL", "WHL", "DTC", "CL1"]:
            if ch not in wb.sheetnames:
                wb.create_sheet(ch)

        sheets = {name: wb[name] for name in wb.sheetnames}
        row_tracker = {ch: 2 for ch in ["RTL", "WHL", "DTC", "CL1"]}
        tc_counter = {ch: 1 for ch in ["RTL", "WHL", "DTC", "CL1"]}

        user_story_id = state["user_story_id"]
        setup_map = state.get("channel_setup", {})
        llm_outputs = state["llm_outputs"]

        logger.info(f"Incoming setup_map keys: {list(setup_map.keys())}")

        for channel, llm_text in llm_outputs.items():

            tc_data = self._parse_llm_output(llm_text)
            ws = sheets[channel]
            row = row_tracker[channel]
            tc_id = f"US_{user_story_id}_TC_{tc_counter[channel]:02d}"

            setup_text = setup_map.get(channel, "")
            precondition = self._format_precondition(channel, setup_text)

            logger.info(f"\nFormatted precondition for {channel}:\n{precondition}\n")

            new_row = self._write_testcase(ws, row, tc_id, tc_data, precondition)

            row_tracker[channel] = new_row
            tc_counter[channel] += 1

        output_file = os.path.join(
            self.output_dir,
            f"Indiv_US_{user_story_id}_Test Scripts_v1.0.xlsx"
        )

        wb.save(output_file)
        logger.info(f" Excel generated: {output_file}")

        # DO NOT REBUILD STATE
        state["excel_output"] = output_file
        return state
