import requests
from urllib.parse import urlparse

# ==============================
# CONFIG
# ==============================

TENANT_ID = "YOUR_TENANT_ID"
CLIENT_ID = "YOUR_CLIENT_ID"
CLIENT_SECRET = "YOUR_CLIENT_SECRET"

SITE_URL = "https://corpofficeapps.sharepoint.com/sites/Ops_Home/nationalops"
FILE_NAME = "sample.txt"   # change this


# ==============================
# GET TOKEN
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
# GET SITE ID
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
# SEARCH ENTIRE LIBRARY
# ==============================

def search_and_read(token, site_id, filename):

    headers = {"Authorization": f"Bearer {token}"}

    # This searches entire drive (all folders)
    search_url = f"https://graph.microsoft.com/v1.0/sites/{site_id}/drive/root/search(q='{filename}')"

    res = requests.get(search_url, headers=headers)
    res.raise_for_status()

    results = res.json().get("value", [])

    if not results:
        print("❌ File not found anywhere in library")
        return

    # If multiple files found
    print(f"🔎 Found {len(results)} matching file(s)\n")

    file_item = results[0]  # take first match

    print("✅ File Name:", file_item["name"])
    print("📁 Path:", file_item["parentReference"]["path"])
    print("🌐 Web URL:", file_item["webUrl"])

    # Read file content in memory
    content_url = f"https://graph.microsoft.com/v1.0/sites/{site_id}/drive/items/{file_item['id']}/content"

    file_res = requests.get(content_url, headers=headers)
    file_res.raise_for_status()

    print("\n======= FILE DATA =======\n")

    try:
        print(file_res.text)
    except:
        print("⚠ Binary file (PDF/DOCX/etc). Cannot print as text.")


# ==============================
# MAIN
# ==============================

if __name__ == "__main__":

    token = get_access_token()
    site_id = get_site_id(token, SITE_URL)

    search_and_read(token, site_id, FILE_NAME)
