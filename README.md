# mcp_server/server.py

from mcp.server.fastmcp import FastMCP
import sys
import os

# allow imports from project root
sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), "..")))

from run_agent import run

mcp = FastMCP("mortgage-ai")

# -------------------------------------------------------
# Tool 1: Generate Testcases
# -------------------------------------------------------
@mcp.tool()
def generate_testcases(work_item_id: str) -> str:
    """
    Generate mortgage test cases from Azure DevOps work item.

    Example:
    Generate testcases for ADO 718521
    """

    try:
        output = run(work_item_id)

        if not output or "excel_output" not in output:
            return "Execution finished but no excel file was produced."

        return f"Testcases generated successfully.\nFile: {output['excel_output']}"

    except Exception as e:
        return f"Execution failed: {str(e)}"


# -------------------------------------------------------
# Tool 2: Only Analyze ADO (debugging tool)
# -------------------------------------------------------
@mcp.tool()
def analyze_story(work_item_id: str) -> str:
    """
    Analyze ADO story without generating testcases.
    Useful for debugging channel detection and preconditions.
    """

    from agents.ado_intelligence_agent import ADOIntelligenceAgent

    state = {"user_story_id": work_item_id}

    agent = ADOIntelligenceAgent()
    result = agent.run(state)

    return f"""
Story {work_item_id} analyzed

Channels: {result.get("channels")}
Title: {result.get("user_story")}
"""


# -------------------------------------------------------
# start server
# -------------------------------------------------------
if __name__ == "__main__":
    mcp.run()
