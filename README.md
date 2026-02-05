from rag_query import TestCaseRAGRetriever
from excel_multi_sheet_exporter import MultiSheetExcelExporter

retriever = TestCaseRAGRetriever()
exporter = MultiSheetExcelExporter("Test_Script_Template_v1.0.xlsx")

# ------------------ INPUTS ------------------
user_story_id = "45678"

user_story = "User should generate Initial Disclosure"
description = "Enter borrower and property details"
ac = "This applies to Retail and DTC channel. Fees must calculate correctly."
# --------------------------------------------

results = retriever.retrieve(user_story, description, ac)

unique_tc = {}

for r in results:
    tc_id = r["testCaseId"]
    if tc_id not in unique_tc:
        full = retriever.rebuild_testcase(tc_id)
        unique_tc[tc_id] = {
            "channel": r["channel"],
            "full_text": full
        }

output_file = f"export/Indiv_US_{user_story_id}_Test Scripts_v1.0.xlsx"

exporter.export(
    list(unique_tc.values()),
    user_story_id,
    output_file
)

print(f"✅ Excel created: {output_file}")
