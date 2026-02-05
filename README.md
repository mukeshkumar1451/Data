from openpyxl import load_workbook


class MultiSheetExcelExporter:

    def __init__(self, template_path):
        self.template_path = template_path

    def export(self, testcases, user_story_id, output_path):
        """
        testcases format expected:
        [
            {
                "channels": ["WHL", "CL1"],
                "scenario": "...",
                "script": "...",
                "precondition": "...",
                "requirement": "...",
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

        # ---------------------------------------------------
        # Each testcase block
        # ---------------------------------------------------
        for tc in testcases:

            generated_tc_id = f"US_{user_story_id}_TC_{tc_counter:02d}"
            tc_counter += 1

            scenario = tc["scenario"]
            script = tc["script"]
            pre = tc["precondition"]
            req = tc["requirement"]
            steps = tc["steps"]
            channels = tc["channels"]  # write to multiple sheets

            # ---------------------------------------------------
            # Write SAME testcase to multiple channel sheets
            # ---------------------------------------------------
            for channel in channels:

                ws = sheets[channel]
                row = row_tracker[channel]

                for idx, step in enumerate(steps):

                    # Write header columns ONLY for first step
                    ws.cell(row, 1).value = generated_tc_id if idx == 0 else ""
                    ws.cell(row, 2).value = scenario if idx == 0 else ""
                    ws.cell(row, 3).value = script if idx == 0 else ""
                    ws.cell(row, 4).value = pre if idx == 0 else ""

                    ws.cell(row, 5).value = step["step_no"]
                    ws.cell(row, 6).value = step["desc"]
                    ws.cell(row, 7).value = step["screen"]
                    ws.cell(row, 8).value = step["data"]
                    ws.cell(row, 9).value = step["expected"]

                    ws.cell(row, 10).value = req if idx == 0 else ""

                    row += 1

                row_tracker[channel] = row

        wb.save(output_path)
