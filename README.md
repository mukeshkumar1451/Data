(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> py test_rag.py

🚀 TRUE Channel-Aware RAG Test Case Generation Started

📥 Loading user story YAML...
✅ YAML loaded

🔍 Detecting channels from Acceptance Criteria...

🧠 Raw detected channels: set()
⚠️ No channel mentioned → Using ALL channels

🔎 Channels detected: ['WHL', 'RTL', 'DTC', 'CL1']


==============================
🔷 Processing Channel: WHL
==============================

🔎 Vector search for channel: WHL
✅ Retrieved 40 chunks before re-ranking

❌ ERROR OCCURRED
LLMReranker.rerank() got an unexpected keyword argument 'search_results'   

📌 TRACEBACK:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 57, in <module>
    results = retriever.retrieve_for_channel(
        user_story,
    ...<2 lines>...
        channel
    )
  File "C:\Users\h84609n\Desktop\VectorDb Test\ragquery\rag_query.py", line 85, in retrieve_for_channel
    reranked = self.reranker.rerank(
        query_text=query_text,
    ...<2 lines>...
        top_n=12
    )
TypeError: LLMReranker.rerank() got an unexpected keyword argument 'search_results'
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> 
