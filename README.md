from rag_query import TestCaseRAGRetriever

retriever = TestCaseRAGRetriever()

user_story = "User should be able to generate initial disclosure"
description = "System should allow entering loan amount and property details"
ac = "Disclosure must calculate fees correctly"

results = retriever.retrieve(user_story, description, ac, channel_filter="RTL")

for r in results[:5]:
    print(r["testCaseId"], r["score"])

# Rebuild a full testcase
tc_text = retriever.rebuild_testcase(results[0]["testCaseId"])
print(tc_text)
