# mcp_client/ado_mcp_client.py

import asyncio
from mcp.client.session import ClientSession
from mcp.client.stdio import stdio_client


class AdoMCPClient:

    def __init__(self):
        self.session = None
        self.client = None

    # --------------------------------------------
    # Start MCP server process and connect
    # --------------------------------------------
    async def connect(self):
        self.client = await stdio_client(
            command="python",
            args=["mcp_tools/ado_server.py"]
        )
        self.session = await ClientSession.create(self.client)

    # --------------------------------------------
    # Call MCP Tool
    # --------------------------------------------
    async def get_user_story(self, work_item_id: str):
        result = await self.session.call_tool(
            "get_user_story",
            {"work_item_id": work_item_id}
        )
        return result.content[0].text

    # --------------------------------------------
    async def close(self):
        if self.session:
            await self.session.close()
        if self.client:
            await self.client.close()


# synchronous wrapper (so your agents can call normally)
def fetch_user_story_via_mcp(work_item_id: str):
    async def _run():
        client = AdoMCPClient()
        await client.connect()
        data = await client.get_user_story(work_item_id)
        await client.close()
        return data

    return asyncio.run(_run())
