# -*- coding: utf-8 -*-
from mcp.server import FastMCP
import json
import logging
import os
import sys

from ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_images

# -------------------------------------------------------------------
# 🔥 FORCE LOGGING CONFIG (REQUIRED FOR MCP / FastMCP)
# -------------------------------------------------------------------

sys.stdout.reconfigure(line_buffering=True)

log_dir = os.path.join(os.path.dirname(os.path.dirname(__file__)), "logs")
os.makedirs(log_dir, exist_ok=True)
log_file = os.path.join(log_dir, "mcp_server.log")

root_logger = logging.getLogger()
root_logger.setLevel(logging.DEBUG)

# Remove existing handlers added by MCP runtime
for h in root_logger.handlers[:]:
    root_logger.removeHandler(h)

formatter = logging.Formatter(
    "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
)

file_handler = logging.FileHandler(log_file, encoding="utf-8")
file_handler.setFormatter(formatter)

console_handler = logging.StreamHandler(sys.stdout)
console_handler.setFormatter(formatter)

root_logger.addHandler(file_handler)
root_logger.addHandler(console_handler)

logger = logging.getLogger(__name__)
logger.info("=" * 80)
logger.info("✅ MCP Custom Logging Initialized")
logger.info("=" * 80)

# -------------------------------------------------------------------
# MCP SERVER
# -------------------------------------------------------------------

mcp = FastMCP("ADO User Story Server")


@mcp.tool()
def get_user_story(user_story_id: str):
    logger.info(f"Fetching user story: {user_story_id}")

    try:
        story = fetch_from_ado(user_story_id)
        logger.debug(f"Fetched story: {story}")

        if not all(k in story for k in ["title", "description", "acceptance_criteria"]):
            raise KeyError("Missing required keys in ADO response")

        clean_ac = process_html_and_images(story["acceptance_criteria"])
        logger.debug(f"Clean AC: {clean_ac}")

        result = {
            "user_story_id": user_story_id,
            "title": story["title"],
            "description": story["description"],
            "acceptance_criteria": clean_ac,
        }

        logger.info(f"Successfully processed user story {user_story_id}")
        return json.dumps(result, indent=2)

    except Exception as e:
        logger.exception(f"Error in get_user_story for {user_story_id}")
        raise


@mcp.tool()
def us_TestcaseGenerator(user_story_id: str):
    logger.info("=" * 80)
    logger.info(f"TEST CASE GENERATION STARTED FOR: {user_story_id}")

    try:
        story = fetch_from_ado(user_story_id)
        logger.debug(f"Fetched story: {story}")

        clean_ac = process_html_and_images(story["acceptance_criteria"])

        logger.info("Starting RAG pipeline...")
        from test_rag_runner import run_rag_pipeline

        output_excel = run_rag_pipeline(
            user_story_id=user_story_id,
            user_story=story["title"],
            description=story["description"],
            ac=clean_ac,
        )

        logger.info(f"Test cases generated: {output_excel}")
        logger.info("=" * 80)
        return f"Test cases generated: {output_excel}"

    except Exception:
        logger.exception("Error during test case generation")
        raise


if __name__ == "__main__":
    logger.info("[*] MCP Server starting...")
    logger.info("[*] Available tools: get_user_story, us_TestcaseGenerator")
    mcp.run()
