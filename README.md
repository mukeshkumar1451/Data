import pandas as pd
import logging
from collections import defaultdict

logger = logging.getLogger(__name__)

def read_excel(file_path):
    xls = pd.ExcelFile(file_path)

    COL_TC = "Test Case ID / Test Script ID"

    # tc -> list of (channel, group)
    tc_map = defaultdict(list)

    for sheet in xls.sheet_names:
        channel = sheet.strip()
        df = pd.read_excel(xls, sheet_name=sheet)
        df.columns = df.columns.str.strip()

        # Fix merged cells
        df[COL_TC] = df[COL_TC].ffill()

        grouped = df.groupby(COL_TC)

        for tc, group in grouped:
            tc_map[tc].append((channel, group))

    # Yield ONE TC with ALL its channels
    for tc, channel_groups in tc_map.items():
        yield tc, channel_groups
