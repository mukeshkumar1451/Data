@mcp.tool()
def us_TestcaseGenerator(user_story_id: str):

    # 1️⃣ Get story from ADO
    story = fetch_from_ado(user_story_id)

    # 2️⃣ Clean HTML + OCR images
    clean_ac = process_html_and_images(story["acceptance_criteria"])

    # 3️⃣ Call your existing RAG pipeline
    from test_rag_runner import run_rag_pipeline

    output_excel = run_rag_pipeline(
        user_story_id=user_story_id,
        user_story=story["title"],
        description=story["description"],
        ac=clean_ac
    )

    return f"✅ Test cases generated: {output_excel}"
