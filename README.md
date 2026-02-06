
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("ado")

@mcp.tool()
def get_user_story(user_story_id: str) -> str:
    return f"User story fetched: {user_story_id}"

if __name__ == "__main__":
    mcp.run()
