import os
import traceback
import yaml

from ragquery.rag_query import TestCaseRAGRetriever as RAGRetriever
from llm.llm_step_parser import parse_llm_steps
from excelexport.excel_multi_sheet_exporter import MultiSheetExcelExporter
from embeddingtovectordb.config import get
from channel_detect.channel_detector import detect_channels


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
        # Step 2 — Detect Channels from AC
        # ---------------------------------------------------
        print("\n🔎 Detecting channels from Acceptance Criteria...")
        channels = detect_channels(ac)
        print(f"✅ Channels to process: {channels}\n")

        # ---------------------------------------------------
        # Step 3 — Initialize Retriever
        # ---------------------------------------------------
        retriever = RAGRetriever()

        all_generated_testcases = []

        # ---------------------------------------------------
        # Step 4 — PROCESS EACH CHANNEL SEPARATELY (IMPORTANT)
        # ---------------------------------------------------
        for channel in channels:

            print(f"\n==============================")
            print(f"🔷 Processing Channel: {channel}")
            print(f"==============================\n")

            # -----------------------------
            # Vector search only for this channel
            # -----------------------------
            print(f"🔍 Running vector search for channel: {channel}")
            results = retriever.retrieve_for_channel(
                user_story,
                description,
                ac,
                channel
            )
            print(f"✅ Retrieved {len(results)} chunks for {channel}\n")

            # -----------------------------
            # Send channel-specific context to LLM
            # -----------------------------
            print(f"🤖 Generating testcase using {channel} historical patterns...\n")

            llm_text = retriever.generate_testcase_with_llm(
                user_story_id=user_story_id,
                user_story=user_story,
                description=description,
                ac=ac,
                retrieved_chunks=results,
                
            )

            print("✅ LLM Response received\n")

            # -----------------------------
            # Parse LLM response into steps
            # -----------------------------
            parsed = parse_llm_steps(llm_text)
            for tc in parsed:
                tc["channels"] = [channel]
            print(f"🧩 Parsed {len(parsed)} testcases for {channel}\n")

            all_generated_testcases.extend(parsed)

        # ---------------------------------------------------
        # Step 5 — Export to Excel
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
