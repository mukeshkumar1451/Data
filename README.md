from openpyxl import load_workbook


class MultiSheetExcelExporter:

    def __init__(self, template_path):
        self.template_path = template_path

    def export(self, testcases, user_story_id, output_path):

        wb = load_workbook(self.template_path)

        for ch in ["RTL", "WHL", "DTC", "CL1"]:
            if ch not in wb.sheetnames:
                wb.create_sheet(ch)

        sheets = {name: wb[name] for name in wb.sheetnames}
        row_tracker = {ch: 2 for ch in ["RTL", "WHL", "DTC", "CL1"]}

        tc_counter = 1

        for tc in testcases:

            generated_tc_id = f"US_{user_story_id}_TC_{tc_counter:02d}"
            scenario_id = f"SC_{tc_counter:02d}"

            scenario = tc["scenario"]
            script = tc["script"]
            pre = tc["precondition"]
            req = tc["requirement"]

            channels = tc["channels"]

            for channel in channels:
                ws = sheets[channel]
                row = row_tracker[channel]

                first_row = True

                for step in tc["steps"]:

                    # Only fill these for first step row
                    ws.cell(row, 1).value = generated_tc_id if first_row else ""
                    ws.cell(row, 2).value = scenario_id if first_row else ""
                    ws.cell(row, 3).value = scenario if first_row else ""
                    ws.cell(row, 4).value = script if first_row else ""

                    ws.cell(row, 5).value = step["step_no"]
                    ws.cell(row, 6).value = step["desc"]
                    ws.cell(row, 7).value = step["screen"]
                    ws.cell(row, 8).value = step["data"]
                    ws.cell(row, 9).value = step["expected"]

                    ws.cell(row, 10).value = req if first_row else ""

                    row += 1
                    first_row = False

                row_tracker[channel] = row

            tc_counter += 1

        wb.save(output_path)
