# main.py
import os
from dotenv import load_dotenv

from config import get_openai_client, get_search_client, get_embed_deployment
from excel_reader import read_testcases_by_sheet
from vector_uploader import upload_testcase
from index_manager import ensure_index_exists

# ========= Load ENV =========
load_dotenv()

EXCEL_PATH = "Indiv_US_718521_Test Scripts_v1.0.xlsx"

def main():
    # ---- Step 1: Ensure index exists ----
    search_endpoint = os.getenv("AZURE_SEARCH_ENDPOINT")
    search_key = os.getenv("AZURE_SEARCH_KEY")
    index_name = os.getenv("AZURE_SEARCH_INDEX")

    ensure_index_exists(search_endpoint, search_key, index_name)

    # ---- Step 2: Create clients ----
    openai_client = get_openai_client()
    search_client = get_search_client()
    embed_deployment = get_embed_deployment()

    print("🚀 Starting Excel → Vector DB upload...\n")

    # ---- Step 3: Process Excel ----
    for channel, test_case_id, group in read_testcases_by_sheet(EXCEL_PATH):
        try:
            upload_testcase(
                openai_client=openai_client,
                search_client=search_client,
                embed_deployment=embed_deployment,
                channel=channel,
                test_case_id=test_case_id,
                group=group
            )
            print(f"✅ Uploaded TestCase '{test_case_id}' from sheet '{channel}'")

        except Exception as e:
            print(f"❌ Error in TestCase '{test_case_id}': {e}")

    print("\n🎉 All testcases uploaded successfully into Azure AI Search vector index")


if __name__ == "__main__":
    main()
