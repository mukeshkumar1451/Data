class LLMReranker:

    def __init__(self, openai, model):
        self.openai = openai
        self.model = model

    def rerank(self, query_text, results):

        scored = []

        for r in results:
            chunk_text = r["content"]

            prompt = f"""
Rate how relevant this historical test step is for the given user story.

User Story:
{query_text}

Historical Chunk:
{chunk_text}

Give ONLY a number between 0 and 1.
"""

            resp = self.openai.chat.completions.create(
                model=self.model,
                messages=[{"role": "user", "content": prompt}],
                temperature=0
            )

            try:
                score = float(resp.choices[0].message.content.strip())
            except:
                score = 0.0

            r["rerank_score"] = score
            scored.append(r)

        # filter below 0.5
        filtered = [x for x in scored if x["rerank_score"] >= 0.5]

        # sort best first
        filtered.sort(key=lambda x: x["rerank_score"], reverse=True)

        return filtered[:12]
