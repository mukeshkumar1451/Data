import os
from dotenv import load_dotenv

# Load environment variables from .env file
load_dotenv()

def get(key, cast=str):
    val=os.getenv(key)
    
    return cast(val) if val and cast != str else val

	---------------------------------------------
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

------------------------------------------
# run_agent.py

import logging

from graph.graph_builder import build_graph

logging.basicConfig(level=logging.INFO)


def run(user_story_id: str):
    app = build_graph()

    initial_state = {        "user_story_id": user_story_id
    }

    final_state = app.invoke(initial_state)

    print("\n Excel Generated at:")
    print(final_state["excel_output"])


if __name__ == "__main__":
    # Example run
    run("718521")
----------------------------------------------------
# utils/html_image_processor.py

import os
import re
import requests
from bs4 import BeautifulSoup
from dotenv import load_dotenv

load_dotenv()
ADO_PAT = os.getenv("ADO_PAT")


# ---------------------------------------------------------
# Download ADO image
# ---------------------------------------------------------
def _download_ado_image(url: str, save_path: str) -> str:
    try:
        response = requests.get(url, auth=("", ADO_PAT))
        response.raise_for_status()

        with open(save_path, "wb") as f:
            f.write(response.content)

        return save_path
    except Exception as e:
        return f"[Image download failed: {e}]"


# ---------------------------------------------------------
# Clean extracted text (VERY IMPORTANT FOR LLM)
# ---------------------------------------------------------
def _normalize_text(text: str) -> str:
    if not text:
        return ""

    #  Fix broken words: sh ould -> should
    text = re.sub(r'(\w)\s{2,}(\w)', r'\1\2', text)

    #  Remove excessive spaces
    text = re.sub(r'[ \t]+', ' ', text)

    #  Join broken lines but keep paragraphs
    text = re.sub(r'\n(?=[a-z])', ' ', text)
    text = re.sub(r'\n{2,}', '\n', text)

    #  Remove dot noise produced by ADO formatting
    text = re.sub(r'\.\s*\.\s*\.\s*', ' ', text)

    #  Remove space before punctuation
    text = re.sub(r'\s+([.,!?])', r'\1', text)

    #  Normalize colon spacing (important for extractors)
    text = re.sub(r'\s*:\s*', ': ', text)

    return text.strip()


# ---------------------------------------------------------
# MAIN FUNCTION
# ---------------------------------------------------------
def process_html_and_download_images(html: str, story_id: str, section: str) -> str:
    """
    Extract readable text + download images from ADO HTML

    section = 'description' or 'ac'
    """

    if not html:
        return ""

    soup = BeautifulSoup(html, "html.parser")

    # remove unwanted tags
    for tag in soup(["script", "style"]):
        tag.decompose()

    # -------------------------------------------------
    # IMAGE DOWNLOAD
    # -------------------------------------------------
    images = soup.find_all("img")

    img_folder = os.path.join("downloads", story_id, section)
    os.makedirs(img_folder, exist_ok=True)

    image_references = []

    for idx, img in enumerate(images, start=1):
        src = img.get("src")
        if not src:
            continue

        save_path = os.path.join(img_folder, f"image_{idx}.png")
        downloaded_path = _download_ado_image(src, save_path)

        #image_references.append(f"[IMAGE DOWNLOADED: {downloaded_path}]")

    # -------------------------------------------------
    # TEXT EXTRACTION
    # -------------------------------------------------
    raw_text = soup.get_text(separator="\n")

    clean_text = _normalize_text(raw_text)

    # -------------------------------------------------
    # APPEND IMAGE REFERENCES
    # -------------------------------------------------
    if image_references:
        final_text = clean_text + "\n\n" + "\n".join(image_references)
    else:
        final_text = clean_text

    return final_text
