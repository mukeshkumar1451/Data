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
            channel = tc["channel"]
            ws = sheets[channel]
            row = row_tracker[channel]

            generated_tc_id = f"US_{user_story_id}_TC_{tc_counter:02d}"
            tc_counter += 1

            lines = tc["full_text"].split("\n")

            scenario = script = pre = req = ""

            for line in lines:
                if line.startswith("Scenario:"):
                    scenario = line.split(":", 1)[1].strip()
                elif line.startswith("Script:"):
                    script = line.split(":", 1)[1].strip()
                elif line.startswith("Precondition:"):
                    pre = line.split(":", 1)[1].strip()
                elif line.startswith("Requirement:"):
                    req = line.split(":", 1)[1].strip()

            for line in lines:
                if line.startswith("Step"):
                    parts = [p.strip() for p in line.split("|")]

                    ws.cell(row, 1).value = generated_tc_id
                    ws.cell(row, 2).value = scenario
                    ws.cell(row, 3).value = script
                    ws.cell(row, 4).value = pre
                    ws.cell(row, 5).value = parts[0]
                    ws.cell(row, 6).value = parts[1] if len(parts) > 1 else ""
                    ws.cell(row, 7).value = parts[2] if len(parts) > 2 else ""
                    ws.cell(row, 8).value = parts[3] if len(parts) > 3 else ""
                    ws.cell(row, 9).value = parts[4] if len(parts) > 4 else ""
                    ws.cell(row, 10).value = req

                    row += 1

            row_tracker[channel] = row

        wb.save(output_path)
