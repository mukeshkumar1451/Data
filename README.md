 TRUE Channel-Aware RAG Test Case Generation Started

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
k_nearest_neighbors is not a known attribute of class <class 'azure.search.documents._generated.models._models_py3.VectorizedQuery'> and will be ignored
✅ Retrieved 50 chunks before re-ranking
✅ 0 chunks after re-ranking
✅ Retrieved 0 chunks for WHL


❌ ERROR OCCURRED
list index out of range

📌 TRACEBACK:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 67, in <module>
    llm_outputs = retriever.generate_testcase_with_llm(
        user_story_id=user_story_id,
    ...<3 lines>...
        retrieved_chunks=results
    )
  File "C:\Users\h84609n\Desktop\VectorDb Test\ragquery\rag_query.py", line 131, in generate_testcase_with_llm
    channel = retrieved_chunks[0]["channel"]
              ~~~~~~~~~~~~~~~~^^^
IndexError: list index out of range
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> 
