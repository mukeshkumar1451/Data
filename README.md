import traceback
import yaml

from rag_query import RAGRetriever
from llm_step_parser import parse_llm_steps
from excel_multi_sheet_exporter import ExcelMultiSheetExporter


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
        llm_response = retriever.generate_testcase_with_llm(
            user_story_id=user_story_id,
            user_story=user_story,
            description=description,
            ac=ac,
            retrieved_chunks=results
        )

        print("✅ LLM Response Received\n")

        # ---------------------------------------------------
        # Step 5 — Parse LLM Steps (VERY IMPORTANT)
        # ---------------------------------------------------
        print("🧩 Parsing LLM steps from response...\n")
        parsed_steps = parse_llm_steps(llm_response)
        print(f"✅ Total steps parsed: {len(parsed_steps)}\n")

        # ---------------------------------------------------
        # Step 6 — Export to Multi-Sheet Excel
        # ---------------------------------------------------
        print("📄 Writing test cases into Excel template...\n")
        exporter = ExcelMultiSheetExporter()
        exporter.export(
            user_story_id=user_story_id,
            parsed_steps=parsed_steps,
            acceptance_criteria=ac
        )

        print("\n🎉 Test Case Excel Generated Successfully!\n")

    except Exception as e:
        print("\n❌ ERROR OCCURRED")
        print(e)
        print("\n📌 TRACEBACK:\n")
        traceback.print_exc()
