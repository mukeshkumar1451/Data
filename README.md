import os
import requests
from dotenv import load_dotenv

load_dotenv()

ORG = os.getenv("ADO_ORG")
PROJECT = os.getenv("ADO_PROJECT")
PAT = os.getenv("ADO_PAT")

def get_user_story(work_item_id: str):
    url = f"https://dev.azure.com/{ORG}/{PROJECT}/_apis/wit/workitems/{work_item_id}?api-version=7.1"

    response = requests.get(
        url,
        auth=("", PAT)
    )
    response.raise_for_status()

    data = response.json()["fields"]

    return {
        "id": work_item_id,
        "title": data.get("System.Title", ""),
        "description": data.get("System.Description", ""),
        "acceptance_criteria": data.get("Microsoft.VSTS.Common.AcceptanceCriteria", "")
    }
