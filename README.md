from rag_query import TestCaseRAGRetriever
from excel_multi_sheet_exporter import MultiSheetExcelExporter
from llm_generator import LLMTestCaseGenerator
from config import get
import yaml, os


def load_userstory(path):
    with open(path, "r") as f:
        return yaml.safe_load(f)


# -------- Load inputs --------
story = load_userstory("userstory_input.yaml")

user_story_id = story["user_story_id"]
user_story = story["user_story"]
description = story["description"]
ac = story["acceptance_criteria"]

# -------- Setup --------
retriever = TestCaseRAGRetriever()
generator = LLMTestCaseGenerator()

template_path = get("EXCEL_TEMPLATE_PATH")
output_dir = get("EXCEL_OUTPUT_DIR")
os.makedirs(output_dir, exist_ok=True)

exporter = MultiSheetExcelExporter(template_path)

# -------- Retrieve historical context --------
results = retriever.retrieve(user_story, description, ac)

context_text = ""
for r in results[:10]:
    full = retriever.rebuild_testcase(r["testCaseId"])
    context_text += full + "\n\n"

# -------- Generate NEW testcases via GPT --------
generated_text = generator.generate(
    user_story,
    description,
    ac,
    context_text
)

# -------- Detect channel from AC --------
channels = ["RTL", "WHL", "DTC", "CL1"]
testcases = []

for ch in channels:
    testcases.append({
        "channel": ch,
        "full_text": generated_text
    })

# -------- Export --------
output_file = os.path.join(
    output_dir,
    f"Indiv_US_{user_story_id}_Test Scripts_v1.0.xlsx"
)

exporter.export(testcases, user_story_id, output_file)

print(f"\n✅ Generated NEW testcases → {output_file}")
