from openpyxl import load_workbook


class MultiSheetExcelExporter:

    def __init__(self, template_path):
        self.template_path = template_path

    def export(self, testcases, user_story_id, output_path):
        """
        testcases format expected from parser:

        [
            {
                "scenario": "...",
                "script": "...",
                "precondition": "...",
                "requirement": "...",
                "channels": ["WHL","CL1"],
                "steps": [
                    {
                        "step_no": "Step 01",
                        "desc": "...",
                        "screen": "...",
                        "data": "...",
                        "expected": "..."
                    }
                ]
            }
        ]
        """

        wb = load_workbook(self.template_path)

        # Ensure all channel sheets exist
        for ch in ["RTL", "WHL", "DTC", "CL1"]:
            if ch not in wb.sheetnames:
                wb.create_sheet(ch)

        sheets = {name: wb[name] for name in wb.sheetnames}
        row_tracker = {ch: 2 for ch in ["RTL", "WHL", "DTC", "CL1"]}

        tc_counter = 1

        for tc in testcases:
            generated_tc_id = f"US_{user_story_id}_TC_{tc_counter:02d}"
            tc_counter += 1

            scenario = tc["scenario"]
            script = tc["script"]
            pre = tc["precondition"]
            req = tc["requirement"]
            channels = tc["channels"]

            # Write SAME testcase into multiple channel sheets
            for channel in channels:

                ws = sheets[channel]
                row = row_tracker[channel]

                for step in tc["steps"]:

                    # ------------------ COLUMN MAPPING (IMPORTANT) ------------------

                    ws.cell(row, 1).value = generated_tc_id               # Test Case ID / Test Script ID
                    ws.cell(row, 2).value = generated_tc_id               # Test Scenario Id
                    ws.cell(row, 3).value = scenario                      # Test Scenario Description
                    ws.cell(row, 4).value = script                        # Test Script Description
                    ws.cell(row, 5).value = pre                           # Pre-Condition & Assumptions
                    ws.cell(row, 6).value = step["step_no"]               # Test Step No.
                    ws.cell(row, 7).value = step["desc"]                  # Test Step Description
                    ws.cell(row, 8).value = step["screen"]                # Screen Name
                    ws.cell(row, 9).value = step["data"]                  # Test Data
                    ws.cell(row, 10).value = step["expected"]             # Expected Results

                    # Leave these blank as per template
                    ws.cell(row, 11).value = ""                           # Actual Results
                    ws.cell(row, 12).value = ""                           # Status
                    ws.cell(row, 13).value = ""                           # Comments
                    ws.cell(row, 14).value = ""                           # Post Condition

                    ws.cell(row, 15).value = req                          # Requirement Mapping

                    row += 1

                row_tracker[channel] = row

        wb.save(output_path)
