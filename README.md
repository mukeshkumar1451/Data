. Context Retrieval 
•	Embed the input User Story using same embedding model.
•	Run the Input User Story against the Vector DB through query (Semantic Search)
o	Test Case embeddings
o	User Story embeddings
o	Meta Tags used to store the embeddings can also be used to drill down the search for more accuracy
o	Retrieve top K=40 combined results.
