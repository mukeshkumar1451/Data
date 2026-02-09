import glob
import logging
from embeddingtovectordb.config import get
from embeddingtovectordb.index_manager import ensure_index

logger = logging.getLogger(__name__)
from embeddingtovectordb.excel_reader import read_excel
from embeddingtovectordb.vector_uploader import upload

ensure_index()

for file in glob.glob(f"{get('EXCEL_INPUT_DIR')}/*.xlsx"):
    logger.info(f"\n📘 Processing file: {file}")

    for sheet, tc, group, steps in read_excel(file):
        upload(sheet, tc, group, steps)

logger.info("🎉 All testcases uploaded into Azure AI Search")
