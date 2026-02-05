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
            for channel in tc["channels"]:

                ws = sheets[channel]
                row = row_tracker[channel]

                generated_tc_id = f"US_{user_story_id}_TC_{tc_counter:02d}"

                first_step = True

                for step in tc["steps"]:

                    if first_step:
                        # Fill scenario info only once
                        ws.cell(row, 1).value = generated_tc_id
                        ws.cell(row, 2).value = tc["scenario"]
                        ws.cell(row, 3).value = tc["script"]
                        ws.cell(row, 4).value = tc["precondition"]
                        first_step = False
                    else:
                        # Leave these blank for next steps
                        ws.cell(row, 1).value = ""
                        ws.cell(row, 2).value = ""
                        ws.cell(row, 3).value = ""
                        ws.cell(row, 4).value = ""

                    # Step columns (correct mapping)
                    ws.cell(row, 5).value = step["step_no"]
                    ws.cell(row, 6).value = step["desc"]
                    ws.cell(row, 7).value = step["screen"]
                    ws.cell(row, 8).value = step["data"]
                    ws.cell(row, 9).value = step["expected"]
                    ws.cell(row, 10).value = tc["requirement"]

                    row += 1

                row_tracker[channel] = row

            tc_counter += 1

        wb.save(output_path)
