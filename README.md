from sentence_transformers import CrossEncoder

class CrossEncoderReranker:

    def __init__(self):
        self.model = CrossEncoder("BAAI/bge-reranker-base")

    def rerank(self, query_text, search_results, threshold=0.5, top_n=12):
        """
        query_text : user story + desc + ac
        search_results : chunks from Azure Search
        """

        pairs = [
            (query_text, r["content"])
            for r in search_results
        ]

        scores = self.model.predict(pairs)

        scored = list(zip(search_results, scores))

        # Filter by threshold
        filtered = [s for s in scored if s[1] >= threshold]

        # Sort best first
        filtered.sort(key=lambda x: x[1], reverse=True)

        # Take top N
        best = [item[0] for item in filtered[:top_n]]

        print(f"✅ Re-ranking reduced {len(search_results)} → {len(best)} best chunks")

        return best
