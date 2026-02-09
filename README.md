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

        # Ensure channel sheets exist
        for ch in ["RTL", "WHL", "DTC", "CL1"]:
            if ch not in wb.sheetnames:
                wb.create_sheet(ch)

        sheets = {name: wb[name] for name in wb.sheetnames}
        row_tracker = {ch: 2 for ch in ["RTL", "WHL", "DTC", "CL1"]}

        # ✅ Channel-wise testcase counters (CRITICAL FIX)
        tc_counter = {
            "RTL": 1,
            "WHL": 1,
            "DTC": 1,
            "CL1": 1
        }

        # ---------------------------------------------------
        # Each testcase block
        # ---------------------------------------------------
        for tc in testcases:

            scenario = tc["scenario"]
            script = tc["script"]
            pre = tc["precondition"]
            req = tc["requirement"]
            steps = tc["steps"]
            channels = tc["channels"]

            # ---------------------------------------------------
            # Write SAME testcase to multiple channel sheets
            # ---------------------------------------------------
            for channel in channels:

                ws = sheets[channel]
                row = row_tracker[channel]

                # ✅ TC id generated PER CHANNEL
                generated_tc_id = f"US_{user_story_id}_TC_{tc_counter[channel]:02d}"

                for idx, step in enumerate(steps):

                    # Header columns only once per testcase
                    ws.cell(row, 1).value = generated_tc_id if idx == 0 else ""
                    ws.cell(row, 2).value = script if idx == 0 else ""
                    ws.cell(row, 3).value = "NA" if idx == 0 else ""
                    ws.cell(row, 4).value = scenario if idx == 0 else ""
                    ws.cell(row, 5).value = pre if idx == 0 else ""

                    ws.cell(row, 6).value = step["step_no"]
                    ws.cell(row, 7).value = step["desc"]
                    ws.cell(row, 8).value = step["screen"]
                    ws.cell(row, 9).value = step["data"]
                    ws.cell(row, 10).value = step["expected"]

                    ws.cell(row, 11).value = req if idx == 0 else ""

                    row += 1

                # update row for this sheet
                row_tracker[channel] = row

                # ✅ increment ONLY this channel counter
                tc_counter[channel] += 1

        wb.save(output_path)
