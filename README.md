# -*- coding: utf-8 -*-
import os
import sys
import traceback
import logging

# Add parent directory to path to import modules
sys.path.insert(0, os.path.dirname(os.path.dirname(os.path.abspath(__file__))))

# Configure logging
log_dir = os.path.join(os.path.dirname(os.path.dirname(__file__)), "logs")
os.makedirs(log_dir, exist_ok=True)
log_file = os.path.join(log_dir, "test_rag_runner.log")

logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler(log_file),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)

from ContextRetrieval_ReRanking.ragquery.rag_query import TestCaseRAGRetriever as RAGRetriever
from ContextRetrieval_ReRanking.llm.llm_step_parser import parse_llm_steps
from ContextRetrieval_ReRanking.excelexport.excel_multi_sheet_exporter import MultiSheetExcelExporter
from embeddingtovectordb.config import get
from ContextRetrieval_ReRanking.channel_detect.channel_detector import detect_channels


def run_rag_pipeline(user_story_id, user_story, description, ac)-> str:
    """
    Execute the RAG pipeline for test case generation.
    
    Args:
        user_story_id: The ID of the user story
        user_story: The user story text
        description: The description of the user story
        ac: The acceptance criteria
        
    Returns:
        str: Path to the generated Excel file
    """
    
    try:
        logger.info("\n" + "="*80)
        logger.info("🚀 TRUE Channel-Aware RAG Test Case Generation Started")
        logger.info("="*80 + "\n")

        # ---------------------------------------------------
        # Step 1 — Detect Channels from AC
        # ---------------------------------------------------
        logger.info("Step 1: Detecting channels from acceptance criteria...")
        channels = detect_channels(ac)
        logger.info(f"🔎 Channels detected: {channels}")
        

        # ---------------------------------------------------
        # Step 2 — Initialize Retriever
        # ---------------------------------------------------
        logger.info("Step 2: Initializing RAG retriever...")
        retriever = RAGRetriever()
        logger.info("✅ RAG retriever initialized")

        all_generated_testcases = []

        # ---------------------------------------------------
        # Step 3 — PROCESS EACH CHANNEL SEPARATELY
        # ---------------------------------------------------
        for channel in channels:
            logger.info(f"\n==============================")
            logger.info(f"🔷 Processing Channel: {channel}")
            logger.info(f"==============================\n")

            # 🔍 Vector search only for this channel
            logger.info(f"Performing vector search for channel {channel}...")
            results = retriever.retrieve_for_channel(
                user_story,
                description,
                ac,
                channel
            )

            logger.info(f"✅ Retrieved {len(results)} chunks for {channel}")

            # 🤖 Generate testcase using this channel history
            logger.info(f"Generating test cases with LLM for channel {channel}...")
            llm_outputs = retriever.generate_testcase_with_llm(
                user_story_id=user_story_id,
                user_story=user_story,
                description=description,
                ac=ac,
                retrieved_chunks=results
            )

            llm_text = llm_outputs[channel]

            logger.info("Parsing LLM response...")
            parsed = parse_llm_steps(llm_text)
            logger.info(f"✅ Parsed {len(parsed)} test cases from LLM output")

            for tc in parsed:
                tc["channels"] = [channel]

            all_generated_testcases.extend(parsed)
            logger.info(f"Total test cases so far: {len(all_generated_testcases)}")

        # ---------------------------------------------------
        # Step 4 — Export to Excel
        # ---------------------------------------------------
        logger.info("\n" + "="*80)
        logger.info("Step 4: Exporting test cases to Excel...")
        logger.info("="*80)
        logger.info("\n📄 Writing channel-specific testcases into Excel template...\n")

        template_path = get("EXCEL_TEMPLATE_PATH")
        output_dir = get("EXCEL_OUTPUT_DIR")
        logger.info(f"Template path: {template_path}")
        logger.info(f"Output directory: {output_dir}")
        
        os.makedirs(output_dir, exist_ok=True)

        output_file = os.path.join(
            output_dir,
            f"Indiv_US_{user_story_id}_Test Scripts_v1.0.xlsx"
        )
        logger.info(f"Output file path: {output_file}")

        exporter = MultiSheetExcelExporter(template_path)
        logger.info(f"Exporting {len(all_generated_testcases)} test cases to Excel...")
        exporter.export(
            testcases=all_generated_testcases,
            user_story_id=user_story_id,
            output_path=output_file
        )

        logger.info(f"\n" + "="*80)
        logger.info(f"🎉 Excel generated successfully!")
        logger.info(f"📁 Output file: {output_file}")
        logger.info("="*80 + "\n")
        
        return output_file

    except Exception as e:
        logger.error("\n" + "="*80)
        logger.error("❌ ERROR OCCURRED IN RAG PIPELINE")
        logger.error("="*80)
        logger.error(f"Error: {e}")
        logger.error("\n📌 TRACEBACK:")
        logger.error(traceback.format_exc())
        logger.error("="*80 + "\n")
        raise
