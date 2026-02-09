\from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from embeddingtovectordb.config import get

search_client = SearchClient(
    endpoint=get("AZURE_SEARCH_ENDPOINT"),
    index_name=get("AZURE_SEARCH_INDEX"),
    credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
)

OUTPUT_FILE = "all_testcases_full_dump.txt"

results = search_client.search(
    search_text="*",
    include_total_count=True
)

total = results.get_count()
print(f"\n🔢 Total documents in index: {total}\n")

with open(OUTPUT_FILE, "w", encoding="utf-8") as f:
    for idx, doc in enumerate(results, start=1):

        header = f"""
============================================================
Document #{idx}
ID          : {doc.get('id')}
TestCaseId  : {doc.get('testCaseId')}
Channels    : {doc.get('channels')}
============================================================
"""

        # Print header
        print(header)
        f.write(header)

        # Print ALL fields dynamically
        for key, value in doc.items():
            if key == "embedding":
                continue  # skip huge vector
            line = f"{key}:\n{value}\n\n"
            print(line)
            f.write(line)

        print("\n")
        f.write("\n")

print(f"\n✅ Full dump saved to: {OUTPUT_FILE}")
