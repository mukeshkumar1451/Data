from rag_query import TestCaseRAGRetriever

def main():
    retriever = TestCaseRAGRetriever()

    # 🔹 Sample inputs (replace with your real User Story)
    user_story = """
    User should be able to generate Initial Disclosure
    after entering loan amount and property details.
    """

    description = """
    System must allow user to input borrower details,
    property value, and loan amount before generating disclosure.
    """

    acceptance_criteria = """
    Disclosure should calculate fees correctly based on loan amount
    and display accurate APR and escrow details.
    """

    print("\n🔍 Running RAG retrieval...\n")

    results = retriever.retrieve(
        user_story=user_story,
        description=description,
        ac=acceptance_criteria,
        channel_filter=None  # or "RTL"
    )

    if not results:
        print("❌ No matches found")
        return

    print(f"✅ Retrieved {len(results)} matching chunks\n")

    # Show top matches
    for r in results[:5]:
        print(
            f"TestCaseId: {r['testCaseId']} | "
            f"Chunk: {r['chunkId']} | "
            f"Channel: {r['channel']} | "
            f"Score: {r['score']:.4f}"
        )

    # 🔹 Rebuild full testcase from best match
    best_tc = results[0]["testCaseId"]

    print(f"\n📄 Rebuilding full testcase for: {best_tc}\n")

    full_testcase = retriever.rebuild_testcase(best_tc)
    print(full_testcase)


if __name__ == "__main__":
    main()
