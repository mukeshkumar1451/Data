import statistics
from typing import List, Dict


# ---------------------------------------------------------
# Extract scores safely
# ---------------------------------------------------------
def _scores(docs: List[Dict]) -> List[float]:
    scores = []
    for d in docs:
        s = d.get("@search.score") or d.get("score") or 0
        if isinstance(s, (int, float)):
            scores.append(float(s))
    return scores


# ---------------------------------------------------------
# Agreement: do top docs say same thing?
# simple lexical overlap
# ---------------------------------------------------------
def _agreement_score(docs: List[Dict]) -> float:
    if len(docs) < 2:
        return 0.5

    texts = [d.get("content", "")[:800].lower() for d in docs[:3]]

    overlaps = []
    for i in range(len(texts)):
        for j in range(i + 1, len(texts)):
            a = set(texts[i].split())
            b = set(texts[j].split())
            if not a or not b:
                continue
            overlaps.append(len(a & b) / max(len(a | b), 1))

    if not overlaps:
        return 0.5

    return statistics.mean(overlaps)


# ---------------------------------------------------------
# Compute final confidence
# ---------------------------------------------------------
def compute_confidence(flow_docs, rule_docs, test_docs) -> int:

    flow_scores = _scores(flow_docs)
    rule_scores = _scores(rule_docs)
    test_scores = _scores(test_docs)

    # relevance (vector similarity)
    relevance = (
        (sum(flow_scores[:3]) / 3 if flow_scores else 0) * 0.4 +
        (sum(rule_scores[:3]) / 3 if rule_scores else 0) * 0.3 +
        (sum(test_scores[:5]) / 5 if test_scores else 0) * 0.3
    )

    # agreement
    agreement = _agreement_score(flow_docs + rule_docs)

    # coverage
    coverage = 0
    coverage += 1 if flow_docs else 0
    coverage += 1 if rule_docs else 0
    coverage += 1 if test_docs else 0
    coverage = coverage / 3

    final_score = (relevance * 0.5 + agreement * 0.3 + coverage * 0.2)

    return int(max(0, min(final_score * 100, 100)))
