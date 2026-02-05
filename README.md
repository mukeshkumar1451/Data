import os
import traceback
import yaml

from rag_query import TestCaseRAGRetriever as RAGRetriever
from llm_step_parser import parse_llm_steps
from excel_multi_sheet_exporter import MultiSheetExcelExporter as ExcelMultiSheetExporter
from embeddingtovectordb.config import get


def load_userstory(path: str):
    print("📥 Loading user story YAML...")
    with open(path, "r", encoding="utf-8") as f:
        data = yaml.safe_load(f)
    print("✅ YAML loaded")
    return data


if __name__ == "__main__":
    try:
        print("\n🚀 RAG Test Case Generation Started\n")

        # ---------------------------------------------------
        # Step 1 — Load User Story Input
        # ---------------------------------------------------
        story = load_userstory("userstory_input.yaml")

        user_story_id = story["user_story_id"]
        user_story = story["user_story"]
        description = story["description"]
        ac = story["acceptance_criteria"]

        # ---------------------------------------------------
        # Step 2 — Initialize Retriever
        # ---------------------------------------------------
        print("\n🔧 Initializing RAG Retriever...")
        retriever = RAGRetriever()
        print("✅ Retriever ready")

        # ---------------------------------------------------
        # Step 3 — Vector Search
        # ---------------------------------------------------
        print("\n🔍 Running vector search in Azure AI Search...\n")
        results = retriever.retrieve(user_story, description, ac)
        print(f"✅ Retrieved {len(results)} vector chunks\n")

        # ---------------------------------------------------
        # Step 4 — Send context to LLM
        # ---------------------------------------------------
        print("🤖 Sending context to Azure OpenAI for test case generation...\n")
        llm_result = retriever.generate_testcase_with_llm(
            user_story_id=user_story_id,
            user_story=user_story,
            description=description,
            ac=ac,
            retrieved_chunks=results
        )
        print("✅ LLM Response Received\n")

        # ---------------------------------------------------
        # Step 5 — Parse LLM Steps
        # ---------------------------------------------------
        print("🧩 Parsing LLM steps from response...\n")
        parsed_steps = parse_llm_steps(llm_response)
        print(f"✅ Total steps parsed: {len(parsed_steps)}\n")

        # ---------------------------------------------------
        # Step 6 — Export to Multi-Sheet Excel
        # ---------------------------------------------------
        print("📄 Writing test cases into Excel template...\n")

        template_path = get("EXCEL_TEMPLATE_PATH")
        output_dir = get("EXCEL_OUTPUT_DIR")

        os.makedirs(output_dir, exist_ok=True)

        output_file = os.path.join(
            output_dir,
            f"Indiv_US_{user_story_id}_Test Scripts_v1.0.xlsx"
        )

        exporter = ExcelMultiSheetExporter(template_path)
        exporter.export(parsed_steps, user_story_id, output_file)

        print(f"\n🎉 Test Case Excel Generated Successfully: {output_file}\n")

    except Exception as e:
        print("\n❌ ERROR OCCURRED")
        print(e)
        print("\n📌 TRACEBACK:\n")
        traceback.print_exc()
