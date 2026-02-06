results_list = list(results)
print(f"✅ Retrieved {len(results_list)} chunks from vector DB")

# ---------------------------------------------
# 🆕 HYBRID STEP — Cross Encoder Re-Ranking
# ---------------------------------------------
query_text = f"""
User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}
"""

reranked = self.reranker.rerank(
    query_text=query_text,
    search_results=results_list,
    threshold=0.5,
    top_n=12
)

return reranked
