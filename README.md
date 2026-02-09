import glob
from config import get
from index_manager import ensure_index
from excel_reader import read_excel
from vector_uploader import upload

ensure_index()

for file in glob.glob(f"{get('EXCEL_INPUT_DIR')}/*.xlsx"):
    for tc, channel_groups in read_excel(file):
        upload(tc, channel_groups)

print("All testcases uploaded into Azure AI Search")
