

❌ ERROR OCCURRED
'TestCaseRAGRetriever' object has no attribute 'openai'

📌 TRACEBACK:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 43, in <module>
    retriever = RAGRetriever()
  File "C:\Users\h84609n\Desktop\VectorDb Test\ragquery\rag_query.py", line 18, in __init__
    self.reranker = LLMReranker(self.openai, self.chat_model)
                                ^^^^^^^^^^^
AttributeError: 'TestCaseRAGRetriever' object has no attribute 'openai'
