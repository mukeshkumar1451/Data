# test_rag.py

import yaml
import traceback
from rag_query import TestCaseRAGRetriever
from llm_generator import LLMTestCaseGenerator


def load_userstory(path):
    print("📥 Loading user story YAML...")
    with open(path, "r", encoding="utf-8") as f:
        return yaml.safe_load(f)


try:
    print("\n🚀 RAG Test Case Generation Started\n")

    # --------------------------------------------------
    # Step 1: Load YAML
    # --------------------------------------------------
    story = load_userstory("userstory_input.yaml")
    print("✅ YAML loaded")

    user_story = story["user_story"]
    description = story["description"]
    ac = story["acceptance_criteria"]

    # --------------------------------------------------
    # Step 2: Initialize Retriever
    # --------------------------------------------------
    print("\n🔧 Initializing RAG Retriever...")
    retriever = TestCaseRAGRetriever()
    print("✅ Retriever ready")

    # --------------------------------------------------
    # Step 3: Vector Retrieval
    # --------------------------------------------------
    print("\n🔍 Running vector search in Azure AI Search...")
    results = retriever.retrieve(user_story, description, ac)
    print(f"✅ Retrieved {len(results)} vector chunks")

    # --------------------------------------------------
    # Step 4: Rebuild historical testcases
    # --------------------------------------------------
    print("\n🧩 Rebuilding historical testcases from chunks...")
    context_text = ""
    for r in results[:5]:
        print(f"   ↳ Rebuilding TestCase: {r['testCaseId']}")
        full = retriever.rebuild_testcase(r["testCaseId"])
        context_text += full + "\n\n"

    print("✅ Context ready for LLM")

    # --------------------------------------------------
    # Step 5: LLM Generation
    # --------------------------------------------------
    print("\n🤖 Sending context to Azure OpenAI for test case generation...")
    generator = LLMTestCaseGenerator()
    generated = generator.generate(user_story, description, ac, context_text)

    print("\n✅ LLM Response Received\n")
    print("----- GENERATED OUTPUT PREVIEW -----\n")
    print(generated[:800])
    print("\n-----------------------------------")

except Exception as e:
    print("\n❌ ERROR OCCURRED")
    print(str(e))
    print("\n📌 TRACEBACK:")
    traceback.print_exc()
