# mcp_tools/qa_mcp_server.py

from mcp.server.fastmcp import FastMCP
import sys
import os
import traceback

# allow project imports
sys.path.append(os.path.dirname(os.path.dirname(__file__)))

from run_agent import run
from agents.ado_intelligence_agent import ADOIntelligenceAgent

mcp = FastMCP("mortgage-ai")

# ------------------------------------------------------------
# Tool 1 — FULL TESTCASE GENERATION
# ------------------------------------------------------------
@mcp.tool()
def generate_testcases(work_item_id: str) -> str:
    """
    Generate full mortgage testcases from an Azure DevOps work item.
    Returns the generated excel path.
    """
    try:
        result = run(work_item_id)
        return f"Testcases generated successfully.\nFile: {result}"
    except Exception:
        return traceback.format_exc()


# ------------------------------------------------------------
# Tool 2 — ONLY ANALYZE USER STORY
# ------------------------------------------------------------
@mcp.tool()
def analyze_userstory(work_item_id: str) -> str:
    """
    Analyze ADO work item and return extracted details (channels, AC, description).
    """
    try:
        agent = ADOIntelligenceAgent()
        state = {"user_story_id": work_item_id}
        output = agent.run(state)

        return f"""
USER STORY ANALYSIS

Channels: {output.get("channels")}
Title: {output.get("user_story")}
Description: {output.get("description")[:800]}
Acceptance Criteria: {output.get("acceptance_criteria")[:800]}
"""
    except Exception:
        return traceback.format_exc()


# ------------------------------------------------------------
# Tool 3 — DEBUG PRECONDITIONS
# ------------------------------------------------------------
@mcp.tool()
def get_preconditions(work_item_id: str) -> str:
    """
    Returns inferred loan setup per channel.
    """
    try:
        from graph.graph_builder import build_graph

        app = build_graph()
        state = {"user_story_id": work_item_id}
        result = app.invoke(state)

        return str(result.get("channel_setup"))
    except Exception:
        return traceback.format_exc()


# ------------------------------------------------------------
# START SERVER
# ------------------------------------------------------------
if __name__ == "__main__":
    mcp.run()
