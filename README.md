import glob
import logging

from embeddingtovectordb.config import get
from embeddingtovectordb.index_manager import ensure_index
from embeddingtovectordb.excel_reader import read_excel
from embeddingtovectordb.vector_uploader import upload

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

ensure_index()

for file in glob.glob(f"{get('EXCEL_INPUT_DIR')}/*.xlsx"):
    logger.info(f"\n📘 Processing file: {file}")

    # NEW: now read_excel yields tc, channel_groups
    for tc, channel_groups in read_excel(file):
        upload(tc, channel_groups)

logger.info("🎉 All testcases uploaded into Azure AI Search")
