# main.py
from config import get_openai_client, get_search_client, get_embed_deployment
from excel_reader import read_testcases_by_sheet
from vector_uploader import upload_testcase

EXCEL_PATH = "Indiv_US_718521_Test Scripts_v1.0.xlsx"

def main():
    openai_client = get_openai_client()
    search_client = get_search_client()
    embed_deployment = get_embed_deployment()

    for channel, test_case_id, group in read_testcases_by_sheet(EXCEL_PATH):
        try:
            upload_testcase(
                openai_client,
                search_client,
                embed_deployment,
                channel,
                test_case_id,
                group
            )
            print(f"✅ Uploaded {test_case_id} from {channel}")
        except Exception as e:
            print(f"❌ Error in {test_case_id}: {e}")

if __name__ == "__main__":
    main()
