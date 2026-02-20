import pandas as pd
from llama_index.core import Document

COL_TC = "Test Case ID / Test Script ID"
COL_STEP = "Test Step No."
COL_ACTION = "Test Step Description"
COL_SCREEN = "Screen Name"
COL_DATA = "Test Data"
COL_EXPECTED = "Expected Results"


def excel_to_documents(file_path):

    xls = pd.ExcelFile(file_path)
    documents = []

    for sheet in xls.sheet_names:
        channel = sheet.strip()

        df = pd.read_excel(xls, sheet_name=sheet)

        # Remove accidental spaces in column headers
        df.columns = df.columns.str.strip()

        # forward fill test case ids
        df[COL_TC] = df[COL_TC].ffill()

        for _, row in df.iterrows():

            step = str(row.get(COL_STEP, "")).strip()

            # Only real steps
            if not step.lower().startswith("step"):
                continue

            action = str(row.get(COL_ACTION, "")).strip()
            screen = str(row.get(COL_SCREEN, "")).strip()
            data = str(row.get(COL_DATA, "")).strip()
            expected = str(row.get(COL_EXPECTED, "")).strip()

            # Semantic text for embedding
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

    return documents
