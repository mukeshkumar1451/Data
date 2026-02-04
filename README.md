import glob
from config import get
from index_manager import ensure_index
from excel_reader import read_excel
from vector_uploader import upload

ensure_index()

for file in glob.glob(f"{get('EXCEL_INPUT_DIR')}/*.xlsx"):
    print(f"\n📘 Processing file: {file}")

    for sheet, tc, group, steps in read_excel(file):
        upload(sheet, tc, group, steps)

print("🎉 All testcases uploaded into Azure AI Search")
---------------------------------------------------
import pandas as pd

def read_excel(file_path):
    xls = pd.ExcelFile(file_path)

    for sheet in xls.sheet_names:
        channel = sheet.strip()   # ✅ THIS IS YOUR CHANNEL

        df = pd.read_excel(xls, sheet_name=sheet)
        df.columns = df.columns.str.strip()

        COL_TC = "Test Case ID / Test Script ID"
        COL_STEP = "Test Step No."

        # 🔥 Fix merged TestCaseId cells
        df[COL_TC] = df[COL_TC].ffill()

        grouped = df.groupby(COL_TC)

        for tc, group in grouped:
            step_count = (
                group[COL_STEP]
                .astype(str)
                .str.strip()
                .str.startswith("Step")
                .sum()
            )

            print(f" sheet='{channel}' | test_case_id='{tc}' | steps={step_count}")

            # ✅ return channel
            yield channel, tc, group, step_count
