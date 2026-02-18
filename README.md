# ado_client.py - Fetches work item data from Azure DevOps using the REST API.
import os
import requests
from dotenv import load_dotenv

load_dotenv()

ORG = os.getenv("ADO_ORG")
PROJECT = os.getenv("ADO_PROJECT")
PAT = os.getenv("ADO_PAT")

def fetch_from_ado(work_item_id: str):
    url = f"https://dev.azure.com/{ORG}/{PROJECT}/_apis/wit/workitems/{work_item_id}?api-version=7.1"

    response = requests.get(
        url,
        auth=("", PAT)
    )
    response.raise_for_status()

    data = response.json()["fields"]

    # Debug log to inspect the fetched data
   # print(f"Fetched data from ADO: {data}")

    # Validate required fields
    required_fields = ["System.Title", "System.Description", "Microsoft.VSTS.Common.AcceptanceCriteria"]
    missing_fields = [field for field in required_fields if field not in data]
    if missing_fields:
        raise KeyError(f"Missing required fields in ADO response: {missing_fields}")

    return {
        "id": work_item_id,
        "title": data.get("System.Title", ""),
        "description": data.get("System.Description", ""),
        "acceptance_criteria": data.get("Microsoft.VSTS.Common.AcceptanceCriteria", "")
    }
