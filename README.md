# mcp_tools/ado_server.py
from mcp.server.fastmcp import FastMCP
from utils.ado_client import fetch_from_ado
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

mcp = FastMCP("ado-intelligence")

# ---------------------------------------------------------
# TOOL: Get User Story From Azure DevOps
# ---------------------------------------------------------
@mcp.tool()
def get_user_story(work_item_id: str) -> dict:
    """
    Fetch a User Story from Azure DevOps by ID.

    Args:
        work_item_id: Azure DevOps Work Item ID

    Returns:
        id, title, description, acceptance_criteria
    """

    logger.info(f"Fetching ADO work item: {work_item_id}")

    try:
        data = fetch_from_ado(work_item_id)

        return {
            "success": True,
            "data": data
        }

    except Exception as e:
        logger.exception("ADO fetch failed")
        return {
            "success": False,
            "error": str(e)
        }


# Required for VS Code MCP runtime
if __name__ == "__main__":
    mcp.run(transport="stdio")
