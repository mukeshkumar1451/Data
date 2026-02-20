import json
import os

PROGRESS_FILE = "ingestion_progress.json"


def load_progress():
    if not os.path.exists(PROGRESS_FILE):
        return set()

    with open(PROGRESS_FILE, "r") as f:
        return set(json.load(f))


def mark_done(file):
    processed = load_progress()
    processed.add(file)

    with open(PROGRESS_FILE, "w") as f:
        json.dump(list(processed), f, indent=2)
        ------------------------
-----------------
import glob
from ingestion.excel_to_documents import excel_to_documents
from ingestion.build_index import build_index
from ingestion.progress_tracker import load_progress, mark_done

files = glob.glob("data/excels/**/*.xlsx", recursive=True)

processed = load_progress()

print(f"Total files found: {len(files)}")
print(f"Already processed: {len(processed)}\n")

for file in files:

    if file in processed:
        print(f"Skipping (already indexed): {file}")
        continue

    print(f"\nProcessing: {file}")

    docs = excel_to_documents(file)

    if not docs:
        print("No valid test steps found")
        mark_done(file)
        continue

    print(f"Uploading {len(docs)} steps to vector DB...")

    build_index(docs)

    mark_done(file)

    print("Completed:", file)

print("\nALL FILES INDEXED SUCCESSFULLY 🚀")


        


        

        
