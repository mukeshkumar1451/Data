# -*- coding: utf-8 -*-
import os
import sys
import traceback
import logging

sys.path.insert(0, os.path.dirname(os.path.dirname(os.path.abspath(__file__))))

logger = logging.getLogger(__name__)

from ContextRetrieval_ReRanking.ragquery.rag_query import TestCaseRAGRetriever as RAGRetriever
from ContextRetrieval_ReRanking.llm.llm_step_parser import parse_llm_steps
from ContextRetrieval_ReRanking.excelexport.excel_multi_sheet_exporter import MultiSheetExcelExporter
from embeddingtovectordb.config import get
from ContextRetrieval_ReRanking.channel_detect.channel_detector import detect_channels


def run_rag_pipeline(user_story_id, user_story, description, ac) -> str:
    try:
        logger.info("🚀 Channel-Aware RAG Test Case Generation Started")

        channels = detect_channels(ac)
        logger.info(f"Channels detected: {channels}")

        retriever = RAGRetriever()

        all_generated_testcases = []

        for channel in channels:
            logger.info(f"Processing Channel: {channel}")

            results = retriever.retrieve_for_channel(
                user_story,
                description,
                ac,
                channel
            )

            llm_outputs = retriever.generate_testcase_with_llm(
                user_story_id=user_story_id,
                user_story=user_story,
                description=description,
                ac=ac,
                retrieved_chunks=results
            )

            parsed = parse_llm_steps(llm_outputs[channel])

            for tc in parsed:
                tc["channels"] = [channel]

            all_generated_testcases.extend(parsed)

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

        logger.info(f"Excel generated: {output_file}")
        return output_file

    except Exception:
        logger.exception("ERROR OCCURRED IN RAG PIPELINE")
        raise
