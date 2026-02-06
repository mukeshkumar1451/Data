import os
import traceback
import yaml

from ragquery.rag_query import TestCaseRAGRetriever as RAGRetriever
from llm.llm_step_parser import parse_llm_steps
from excelexport.excel_multi_sheet_exporter import MultiSheetExcelExporter
from embeddingtovectordb.config import get


def load_userstory(path: str):
    print("📥 Loading user story YAML...")
    with open(path, "r", encoding="utf-8") as f:
        data = yaml.safe_load(f)
    print("✅ YAML loaded")
    return data


if __name__ == "__main__":
    try:
        print("\n🚀 TRUE Channel-Aware RAG Test Case Generation Started\n")

        # ---------------------------------------------------
        # Step 1 — Load User Story
        # ---------------------------------------------------
        story = load_userstory("userstory_input.yaml")

        user_story_id = story["user_story_id"]
        user_story = story["user_story"]
        description = story["description"]
        ac = story["acceptance_criteria"]

        # ---------------------------------------------------
        # Step 2 — Initialize Retriever
        # ---------------------------------------------------
        retriever = RAGRetriever()

        # ---------------------------------------------------
        # Step 3 — Vector Search (all channels)
        # ---------------------------------------------------
        print("\n🔍 Running vector search in Azure AI Search...\n")
        results = retriever.retrieve(
            user_story,
            description,
            ac
        )
        print(f"✅ Retrieved {len(results)} vector chunks\n")

        # ---------------------------------------------------
        # Step 4 — Channel-wise LLM generation (inside rag_query)
        # ---------------------------------------------------
        print("🤖 Generating testcases per channel using historical patterns...\n")

        channel_llm_outputs = retriever.generate_testcase_with_llm(
            user_story_id=user_story_id,
            user_story=user_story,
            description=description,
            ac=ac,
            retrieved_chunks=results
        )

        print("✅ LLM responses received for channels\n")

        # ---------------------------------------------------
        # Step 5 — Parse LLM output
        # ---------------------------------------------------
        all_generated_testcases = []

        for channel, llm_text in channel_llm_outputs.items():
            print(f"🧩 Parsing testcase for channel: {channel}")

            parsed = parse_llm_steps(llm_text)

            for tc in parsed:
                tc["channels"] = [channel]

            all_generated_testcases.extend(parsed)

        # ---------------------------------------------------
        # Step 6 — Export to Excel
        # ---------------------------------------------------
        print("\n📄 Writing channel-specific testcases into Excel template...\n")

        template_path = get("EXCEL_TEMPLATE_PATH")
        output_dir = get("EXCEL_OUTPUT_DIR")
        os.makedirs(output_dir, exist_ok=True)

        output_file = os.path.join(
            output_dir,
            f"Indiv_US_{user_story_id}_Test Scripts_v1.0.xlsx"
        )

        exporter = MultiSheetExcelExporter(template_path)
        exporter.export(
            testcases=all_generated_testcases,
            user_story_id=user_story_id,
            output_path=output_file
        )

        print(f"\n🎉 Excel generated successfully:\n{output_file}\n")

    except Exception as e:
        print("\n❌ ERROR OCCURRED")
        print(e)
        print("\n📌 TRACEBACK:\n")
        traceback.print_exc()
