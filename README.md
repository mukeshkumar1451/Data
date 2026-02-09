# -*- coding: utf-8 -*-
from mcp.server import FastMCP
import json
import logging
import os
from ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_images

# Configure logging
log_dir = os.path.join(os.path.dirname(os.path.dirname(__file__)), "logs")
os.makedirs(log_dir, exist_ok=True)
log_file = os.path.join(log_dir, "mcp_server.log")

logging.basicConfig(
    level=logging.INFO,  # Changed from DEBUG to INFO
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler(log_file),
        
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)
logger.info("=" * 80)
logger.info("MCP Server Started")

logger.info("=" * 80)

# Create MCP server instance
mcp = FastMCP("ADO User Story Server")

@mcp.tool()
def get_user_story(user_story_id: str):
    """
    Fetches a user story from Azure DevOps and returns structured data.
    
    Args:
        user_story_id: The ID of the user story to fetch
        
    Returns:
        JSON string with user_story_id, title, description, and cleaned acceptance_criteria
    """
    progress_messages = []
    logger.info(f"Fetching user story: {user_story_id}")
    progress_messages.append(f" Fetching user story {user_story_id} from Azure DevOps...")
    try:
        # Get story from ADO
        story = fetch_from_ado(user_story_id)
        logger.info(f"Successfully fetched story: {story['title']}")
        
        # Debug log to inspect the fetched story
        logger.debug(f"Fetched story content: {story}")

        # Ensure required keys exist in the story dictionary
        if not all(key in story for key in ['title', 'description', 'acceptance_criteria']):
            raise KeyError("The fetched story is missing one or more required keys: 'title', 'description', 'acceptance_criteria'")
        
        # Clean HTML + OCR images from acceptance criteria
        clean_ac = process_html_and_images(story["acceptance_criteria"])
        logger.info(f"HTML processing complete for {user_story_id}")
        
        # Debug log for acceptance criteria processing
        logger.debug(f"Acceptance criteria before processing: {story['acceptance_criteria']}")
        logger.debug(f"Cleaned acceptance criteria: {clean_ac}")

        # Build structured response
        result = {
            "user_story_id": user_story_id,
            "title": story["title"],
            "description": story["description"],
            "acceptance_criteria": clean_ac
        }
        
        logger.info(f"Successfully processed user story {user_story_id}")
        return json.dumps(result, indent=2)
    except Exception as e:
        logger.error(f"Error fetching user story {user_story_id}: {str(e)}")
        raise

@mcp.tool()
def us_TestcaseGenerator(user_story_id: str):
    """
    This tool performs end-to-end automated test case generation from an Azure DevOps User Story.

    Given a user_story_id, the tool will:

    1. Fetch the user story from Azure DevOps (title, description, acceptance criteria with HTML and images).
    2. Extract images from the acceptance criteria and perform OCR to capture UI flow text.
    3. Clean and merge OCR text with acceptance criteria.
    4. Detect applicable channels (RTL, WHL, DTC, CL1) from the acceptance criteria.
    5. Perform semantic vector search in Azure AI Search for historical test cases.
    6. Apply hybrid retrieval with LLM re-ranking for best matching test cases.
    7. Generate new channel-specific test cases using Azure OpenAI based on historical patterns.
    8. Export the generated test cases into a multi-sheet Excel file using the project template.

    Args:
        user_story_id: The ID of the user story to process

    Returns:
        A confirmation message with the path to the generated Excel test script file saved in the project output folder.
    """
    logger.info(f"=" * 80)
    logger.info(f"TEST CASE GENERATION STARTED FOR USER STORY: {user_story_id}")
    logger.info(f"=" * 80)
    
    try:
        # Get story from ADO
        logger.info(f"📥 Fetching user story {user_story_id} from Azure DevOps...")
        story = fetch_from_ado(user_story_id)
        logger.info(f"✅ Successfully fetched user story: {story['title']}")

        # Debug log to inspect the fetched story
        logger.debug(f"Fetched story content: {story}")

        # Ensure required keys exist in the story dictionary
        if not all(key in story for key in ['title', 'description', 'acceptance_criteria']):
            raise KeyError("The fetched story is missing one or more required keys: 'title', 'description', 'acceptance_criteria'")

        # Clean HTML + OCR images
        logger.info(f"🖼️  Processing HTML and extracting images from acceptance criteria...")
        clean_ac = process_html_and_images(story["acceptance_criteria"])
        logger.info(f"✅ HTML processing and OCR complete")

        # Debug log for acceptance criteria processing
        logger.debug(f"Acceptance criteria before processing: {story['acceptance_criteria']}")
        logger.debug(f"Cleaned acceptance criteria: {clean_ac}")

        # Debug log for RAG pipeline inputs
        logger.debug(f"RAG pipeline inputs: user_story_id={user_story_id}, user_story={story['title']}, description={story['description']}, ac={clean_ac}")

        # Call your existing RAG pipeline
        logger.info(f"🚀 Starting RAG pipeline for test case generation...")
        from test_rag_runner import run_rag_pipeline

        output_excel = run_rag_pipeline(
            user_story_id=user_story_id,
            user_story=story["title"],
            description=story["description"],
            ac=clean_ac
        )
        
        logger.info(f"=" * 80)
        logger.info(f"✅ TEST CASE GENERATION COMPLETED SUCCESSFULLY")
        logger.info(f"📁 Output file: {output_excel}")
        logger.info(f"=" * 80)

        return f"Test cases generated: {output_excel}"
        
    except Exception as e:
        logger.error(f"=" * 80)
        logger.error(f"❌ ERROR DURING TEST CASE GENERATION FOR USER STORY: {user_story_id}")
        logger.error(f"Error details: {str(e)}")
        logger.error(f"=" * 80)
        import traceback
        logger.error(traceback.format_exc())
        raise

if __name__ == "__main__":
    import sys
    import logging
    logger = logging.getLogger(__name__)
    logger.info("[*] MCP Server starting...")
    logger.info("[*] Server location: adomcpserver/server.py")
    logger.info("[*] MCP Server is RUNNING and waiting for connections")
    logger.info("[*] Available tools: get_user_story, us_TestcaseGenerator")
    mcp.run()
