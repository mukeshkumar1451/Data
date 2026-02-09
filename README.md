from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from embeddingtovectordb.config import get

search_client = SearchClient(
    endpoint=get("AZURE_SEARCH_ENDPOINT"),
    index_name=get("AZURE_SEARCH_INDEX"),
    credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
)

OUTPUT_FILE = "all_testcases_dump.txt"

results = search_client.search(
    search_text="*",
    top=1000,                 # enough to fetch all docs
    include_total_count=True
)

print(f"\nTotal documents in index: {results.get_count()}\n")

with open(OUTPUT_FILE, "w", encoding="utf-8") as f:
    for i, doc in enumerate(results, start=1):
        header = f"""
====================================================
Document #{i}
TestCaseId : {doc.get('testCaseId')}
Channels   : {doc.get('channels')}
====================================================
"""
        content = doc.get("content", "")

        # Print to console
        print(header)
        print(content)

        # Write to file
        f.write(header)
        f.write(content)
        f.write("\n\n")

print(f"\n✅ All testcases saved to file: {OUTPUT_FILE}")
