class LLMReranker:

    def __init__(self, openai_client, chat_model):
        self.openai = openai_client
        self.chat_model = chat_model

    def rerank(self, query_text, chunks, top_n=12):
        prompt = f"""
You are ranking historical test case chunks by relevance.

User Story Context:
{query_text}

Below are candidate chunks:

{self._format_chunks(chunks)}

Return ONLY the IDs of the most relevant chunks in order.
"""

        resp = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        ranked_ids = resp.choices[0].message.content.strip().split(",")

        id_map = {str(i): c for i, c in enumerate(chunks)}
        return [id_map[i.strip()] for i in ranked_ids if i.strip() in id_map]

    def _format_chunks(self, chunks):
        text = ""
        for i, c in enumerate(chunks):
            text += f"\nID {i}: {c['content'][:800]}"
        return text
