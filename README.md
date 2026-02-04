# excel_reader.py
import pandas as pd

def read_testcases_by_sheet(excel_path):
    xls = pd.ExcelFile(excel_path)

    for sheet in xls.sheet_names:
        df = pd.read_excel(xls, sheet_name=sheet)
        df.columns = df.columns.str.strip()

        channel = sheet.strip()

        COL_TESTCASE = "Test Case ID / Test Script ID"
        grouped = df.groupby(COL_TESTCASE)

        for test_case_id, group in grouped:
            yield channel, test_case_id, group
