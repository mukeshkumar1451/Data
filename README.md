import requests
from urllib.parse import urlparse
from io import BytesIO
from docx import Document

# ==============================
# CONFIGURATION
# ==============================

TENANT_ID = "YOUR_TENANT_ID"
CLIENT_ID = "YOUR_CLIENT_ID"
CLIENT_SECRET = "YOUR_CLIENT_SECRET"

SITE_URL = "https://corpofficeapps.sharepoint.com/sites/Ops_Home/nationalops"

# Exact file name
FILE_NAME = "sKnowledge Modelling Approach Document.docx"


# ==============================
# GET ACCESS TOKEN
# ==============================

def get_access_token():
    token_url = f"https://login.microsoftonline.com/{TENANT_ID}/oauth2/v2.0/token"

    data = {
        "client_id": CLIENT_ID,
        "scope": "https://graph.microsoft.com/.default",
        "client_secret": CLIENT_SECRET,
        "grant_type": "client_credentials",
    }

    res = requests.post(token_url, data=data)
    res.raise_for_status()

    print("✅ Token acquired\n")
    return res.json()["access_token"]


# ==============================
# GET SITE ID FROM URL
# ==============================

def get_site_id(token, site_url):
    headers = {"Authorization": f"Bearer {token}"}

    parsed = urlparse(site_url)
    host = parsed.netloc
    path = parsed.path

    graph_url = f"https://graph.microsoft.com/v1.0/sites/{host}:{path}"

    res = requests.get(graph_url, headers=headers)
    res.raise_for_status()

    site_data = res.json()

    print("✅ Site:", site_data["displayName"])
    return site_data["id"]


# ==============================
# SEARCH FILE (SAFE WAY)
# ==============================

def search_and_read_docx(token, site_id, filename):

    headers = {"Authorization": f"Bearer {token}"}

    # Use only first word as keyword (prevents Graph 500)
    keyword = filename.split()[0]

    search_url = (
        f"https://graph.microsoft.com/v1.0/"
        f"sites/{site_id}/drive/root/search(q='{keyword}')"
    )

    res = requests.get(search_url, headers=headers)

    if res.status_code != 200:
        print("❌ Search failed")
        print("Status:", res.status_code)
        print(res.text)
        return

    results = res.json().get("value", [])

    if not results:
        print("❌ No matching files found")
        return

    # Filter exact filename locally
    file_item = None
    for item in results:
        if item.get("name", "").lower() == filename.lower():
            file_item = item
            break

    if not file_item:
        print("❌ Exact file not found. Available matches:")
        for r in results:
            print(" -", r["name"])
        return

    print("✅ File Found:", file_item["name"])
    print("📁 Path:", file_item["parentReference"]["path"])
    print("🌐 URL:", file_item["webUrl"])

    # Read file in memory
    content_url = (
        f"https://graph.microsoft.com/v1.0/"
        f"sites/{site_id}/drive/items/{file_item['id']}/content"
    )

    file_res = requests.get(content_url, headers=headers)
    file_res.raise_for_status()

    # Extract DOCX in memory
    doc = Document(BytesIO(file_res.content))

    print("\n======= DOCX CONTENT =======\n")

    for para in doc.paragraphs:
        if para.text.strip():
            print(para.text)


# ==============================
# MAIN
# ==============================

if __name__ == "__main__":

    token = get_access_token()
    site_id = get_site_id(token, SITE_URL)

    search_and_read_docx(token, site_id, FILE_NAME)
