def retrieve_for_channel(self, user_story, description, ac, channel):

    print(f"\n🔎 Hybrid search for channel: {channel}")

    query_text = f"""
User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}
"""

    query_vector = self.embed_query(query_text)

    vector_query = VectorizedQuery(
        kind="vector",
        vector=query_vector,
        k=self.top_k,
        fields="embedding"
    )

    # 🔥 HYBRID SEARCH (text + vector)
    results = self.search_client.search(
        search_text=query_text,   # <-- TEXT SEARCH ADDED
        vector_queries=[vector_query],
        filter=f"channel eq '{channel}'",
        select=[
            "testCaseId",
            "chunkId",
            "content",
            "channel",
            "@search.score"       # <-- VECTOR/TEXT SCORE
        ],
        top=self.top_k
    )

    results_list = list(results)

    print(f"✅ Retrieved {len(results_list)} chunks before re-ranking")

    # Show raw scores
    for r in results_list[:5]:
        print(f"   📊 Raw Score: {r.get('@search.score'):.4f} | TC: {r['testCaseId']} | Chunk: {r['chunkId']}")

    # ---------------- Re-ranking ----------------
    reranked = self.reranker.rerank(
        query_text,
        results_list
    )

    print("\n🔁 After LLM Re-ranking:\n")

    for r in reranked:
        print(
            f"   🧠 Rerank Score: {r['rerank_score']:.3f} | "
            f"Vector Score: {r.get('@search.score'):.4f} | "
            f"TC: {r['testCaseId']} | Chunk: {r['chunkId']}"
        )

    return reranked
