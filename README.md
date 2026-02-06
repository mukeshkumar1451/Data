import requests


class MCPClient:

    def __init__(self, base_url="http://localhost:8000"):
        self.base_url = base_url

    def get_enriched_user_story(self, story_id: str):
        print(f"🌐 Fetching enriched story from MCP: {story_id}")

        url = f"{self.base_url}/userstory/{story_id}/enriched"
        res = requests.get(url)

        if res.status_code != 200:
            raise Exception(f"MCP error: {res.text}")

        data = res.json()

        return {
            "user_story_id": story_id,
            "user_story": data["title"],
            "description": data["description"],
            "acceptance_criteria": data["acceptance_criteria_enriched"]
        }
