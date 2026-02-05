from openpyxl import load_workbook


class MultiSheetExcelExporter:
    """
    Writes parsed LLM testcases into multi-sheet Excel template.

    Expected testcase structure from llm_step_parser:

    [
        {
            "scenario": "...",
            "script": "...",
            "precondition": "...",
            "requirement": "...",
            "channels": ["WHL", "CL1"],
            "steps": [
                {
                    "step_no": "Step 01",
                    "action": "...",
                    "screen": "...",
                    "testdata": "...",
                    "expected": "..."
                }
            ]
        }
    ]
    """

    def __init__(self, template_path: str):
        self.template_path = template_path

    # ---------------------------------------------------------
    # Main Export Function
    # ---------------------------------------------------------
    def export(self, testcases, user_story_id, output_path):

        wb = load_workbook(self.template_path)

        # Ensure all channel sheets exist
        for ch in ["RTL", "WHL", "DTC", "CL1"]:
            if ch not in wb.sheetnames:
                wb.create_sheet(ch)

        sheets = {name: wb[name] for name in wb.sheetnames}

        # Track row position per sheet (row 1 = headers)
        row_tracker = {ch: 2 for ch in ["RTL", "WHL", "DTC", "CL1"]}

        tc_counter = 1

        for tc in testcases:

            generated_tc_id = f"US_{user_story_id}_TC_{tc_counter:02d}"

            scenario = tc["scenario"]
            script = tc["script"]
            precondition = tc["precondition"]
            requirement = tc["requirement"]
            channels = tc["channels"]

            # -------------------------------------------------
            # Write SAME testcase into MULTIPLE sheets
            # -------------------------------------------------
            for channel in channels:
                ws = sheets[channel]
                row = row_tracker[channel]

                for step in tc["steps"]:
                    ws.cell(row, 1).value = generated_tc_id
                    ws.cell(row, 2).value = scenario
                    ws.cell(row, 3).value = script
                    ws.cell(row, 4).value = precondition
                    ws.cell(row, 5).value = step["step_no"]
                    ws.cell(row, 6).value = step["action"]
                    ws.cell(row, 7).value = step["screen"]
                    ws.cell(row, 8).value = step["testdata"]
                    ws.cell(row, 9).value = step["expected"]
                    ws.cell(row, 10).value = requirement

                    row += 1

                row_tracker[channel] = row

            tc_counter += 1

        wb.save(output_path)
