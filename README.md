# -*- coding: utf-8 -*-
import os
import sys
import traceback
import logging

# Add parent directory to path to import modules
sys.path.insert(0, os.path.dirname(os.path.dirname(os.path.abspath(__file__))))

# -------------------------------------------------------------------
# ✅ MCP-SAFE LOGGING (DO NOT USE basicConfig HERE)
# -------------------------------------------------------------------

log_dir = os.path.join(os.path.dirname(os.path.dirname(__file__)), "logs")
os.makedirs(log_dir, exist_ok=True)
log_file = os.path.join(log_dir, "test_rag_runner.log")

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

# Prevent duplicate handlers if re-imported
if not any(isinstance(h, logging.FileHandler) and h.baseFilename == log_file
           for h in logger.handlers):
    
    file_handler = logging.FileHandler(log_file, encoding="utf-8")
    formatter = logging.Formatter(
        "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
    )
    file_handler.setFormatter(formatter)
    logger.addHandler(file_handler)

logger.propagate = True  # send logs to root logger (console + main file)

# -------------------------------------------------------------------

from ContextRetrieval_ReRanking.ragquery.rag_query import TestCaseRAGRetriever as RAGRetriever
from ContextRetrieval_ReRanking.llm.llm_step_parser import parse_llm_steps
from ContextRetrieval_ReRanking.excelexport.excel_multi_sheet_exporter import MultiSheetExcelExporter
from embeddingtovectordb.config import get
from ContextRetrieval_ReRanking.channel_detect.channel_detector import detect_channels


def run_rag_pipeline(user_story_id, user_story, description, ac) -> str:
    try:
        logger.info("\n" + "="*80)
        logger.info("🚀 TRUE Channel-Aware RAG Test Case Generation Started")
        logger.info("="*80 + "\n")

        # Step 1 — Detect Channels
        logger.info("Step 1: Detecting channels from acceptance criteria...")
        channels = detect_channels(ac)
        logger.info(f"🔎 Channels detected: {channels}")

        # Step 2 — Initialize Retriever
        logger.info("Step 2: Initializing RAG retriever...")
        retriever = RAGRetriever()
        logger.info("✅ RAG retriever initialized")

        all_generated_testcases = []

        # Step 3 — Process each channel
        for channel in channels:
            logger.info(f"\n🔷 Processing Channel: {channel}")

            results = retriever.retrieve_for_channel(
                user_story,
                description,
                ac,
                channel
            )
            logger.info(f"✅ Retrieved {len(results)} chunks for {channel}")

            llm_outputs = retriever.generate_testcase_with_llm(
                user_story_id=user_story_id,
                user_story=user_story,
                description=description,
                ac=ac,
                retrieved_chunks=results
            )

            llm_text = llm_outputs[channel]

            parsed = parse_llm_steps(llm_text)
            logger.info(f"✅ Parsed {len(parsed)} test cases")

            for tc in parsed:
                tc["channels"] = [channel]

            all_generated_testcases.extend(parsed)

        # Step 4 — Export to Excel
        logger.info("Step 4: Exporting test cases to Excel...")

        template_path = get("EXCEL_TEMPLATE_PATH")
        output_dir = get("EXCEL_OUTPUT_DIR")
        os.makedirs(output_dir, exist_ok=True)

        output_file = os.path.join(
            output_dir,
            f"Indiv_US_{user_story_id}_Test Scripts_v1.0.xlsx"
        )

        exporter = MultiSheetExcelExporter(template_path)
        exporter.export(
            testcases=all_generated_testcases,
            user_story_id=user_story_id,
            output_path=output_file
        )

        logger.info(f"🎉 Excel generated: {output_file}")
        return output_file

    except Exception:
        logger.exception("❌ ERROR OCCURRED IN RAG PIPELINE")
        raise
