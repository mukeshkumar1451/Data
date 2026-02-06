=============================
🔷 Processing Channel: WHL
==============================


🔎 Vector search for channel: WHL
k_nearest_neighbors is not a known attribute of class <class 'azure.search.documents._generated.models._models_py3.VectorizedQuery'> and will be ignored
✅ Retrieved 50 chunks before re-ranking

❌ ERROR OCCURRED
LLMReranker.rerank() got an unexpected keyword argument 'results'

📌 TRACEBACK:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 57, in <module>
    results = retriever.retrieve_for_channel(
        user_story,
    ...<2 lines>...
        channel
    )
  File "C:\Users\h84609n\Desktop\VectorDb Test\ragquery\rag_query.py", line 84, in retrieve_for_channel
    reranked = self.reranker.rerank(
        query_text=query_text,
    ...<2 lines>...
        top_n=12
    )
TypeError: LLMReranker.rerank() got an unexpected keyword argument 'results'
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> 
