import pandas as pd
from llama_index.core import Document


REQUIRED_COLUMNS = [
    "Test Case ID / Test Script ID",
    "Test Step No.",
    "Test Step Description",
    "Expected Results",
]


def is_test_script(df: pd.DataFrame) -> bool:
    cols = [c.strip().lower() for c in df.columns]

    for required in REQUIRED_COLUMNS:
        if required.lower() not in cols:
            return False
    return True


def excel_to_documents(file_path):

    xls = pd.ExcelFile(file_path)
    documents = []

    print(f"Scanning file: {file_path}")

    for sheet in xls.sheet_names:

        df = pd.read_excel(xls, sheet_name=sheet)
        df.columns = df.columns.str.strip()

        # -------- Detect correct excel type --------
        if not is_test_script(df):
            print(f"  Skipping sheet '{sheet}' (Not a Test Script format)")
            continue

        print(f"  Processing sheet '{sheet}'")

        channel = sheet.strip()

        COL_TC = "Test Case ID / Test Script ID"
        COL_STEP = "Test Step No."
        COL_ACTION = "Test Step Description"
        COL_SCREEN = "Screen Name"
        COL_DATA = "Test Data"
        COL_EXPECTED = "Expected Results"

        df[COL_TC] = df[COL_TC].ffill()

        for _, row in df.iterrows():

            step = str(row.get(COL_STEP, "")).strip()
            if not step.lower().startswith("step"):
                continue

            action = str(row.get(COL_ACTION, "")).strip()
            screen = str(row.get(COL_SCREEN, "")).strip()
            data = str(row.get(COL_DATA, "")).strip()
            expected = str(row.get(COL_EXPECTED, "")).strip()

            text = f"""
User performs action: {action}
On screen: {screen}
Using data: {data}
System should result: {expected}
"""

            metadata = {
                "channel": channel,
                "testCaseId": str(row[COL_TC]),
                "stepNo": step,
                "screen": screen,
            }

            documents.append(Document(text=text, metadata=metadata))

    print(f"Extracted {len(documents)} steps from {file_path}\n")

    return documents
