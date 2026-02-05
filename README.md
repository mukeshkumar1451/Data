from openpyxl import load_workbook


class MultiSheetExcelExporter:

    def __init__(self, template_path):
        self.template_path = template_path

    def export(self, testcases, user_story_id, output_path):
        """
        testcases format expected:
        [
            {
                "channel": "WHL",
                "scenario": "...",
                "script": "...",
                "precondition": "...",
                "requirement": "...",
                "steps": [ {step_no, action, screen, testdata, expected} ]
            }
        ]
        """

        wb = load_workbook(self.template_path)

        for ch in ["RTL", "WHL", "DTC", "CL1"]:
            if ch not in wb.sheetnames:
                wb.create_sheet(ch)

        sheets = {name: wb[name] for name in wb.sheetnames}
        row_tracker = {ch: 2 for ch in ["RTL", "WHL", "DTC", "CL1"]}

        tc_counter = 1

        for tc in testcases:
            channel = tc["channel"]
            ws = sheets[channel]
            row = row_tracker[channel]

            generated_tc_id = f"US_{user_story_id}_TC_{tc_counter:02d}"
            tc_counter += 1

            scenario = tc["scenario"]
            script = tc["script"]
            pre = tc["precondition"]
            req = tc["requirement"]

            for step in tc["steps"]:
                ws.cell(row, 1).value = generated_tc_id
                ws.cell(row, 2).value = scenario
                ws.cell(row, 3).value = script
                ws.cell(row, 4).value = pre
                ws.cell(row, 5).value = step["step_no"]
                ws.cell(row, 6).value = step["action"]
                ws.cell(row, 7).value = step["screen"]
                ws.cell(row, 8).value = step["testdata"]
                ws.cell(row, 9).value = step["expected"]
                ws.cell(row, 10).value = req
                row += 1

            row_tracker[channel] = row

        wb.save(output_path)

