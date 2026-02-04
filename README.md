if a test case has very large content then combining everything into one giant chunk can be tough because for Over‑large chunks the embeddings will fail and the LLM cannot process it as well. So for such larger test cases alone split large fields into multiple sub chunks per test cases, it is good because we can preserve all information. 
 
for a very large test case that is exceeding embedding model limit or model’s max token limit, we should do like this 
