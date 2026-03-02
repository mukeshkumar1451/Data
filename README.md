import requests

# ==============================
# CONFIGURATION
# ==============================

TENANT_ID = "YOUR_TENANT_ID"
CLIENT_ID = "YOUR_CLIENT_ID"
CLIENT_SECRET = "YOUR_CLIENT_SECRET"

SITE_URL = "https://corpofficeapps.sharepoint.com/sites/Ops_Home/nationalops"

FILE_NAME = "sample.txt"   # change to your file


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

    response = requests.post(token_url, data=data)
    response.raise_for_status()

    print("✅ Access token acquired\n")
    return response.json()["access_token"]


# ==============================
# GET SITE ID FROM URL
# ==============================

def get_site_id(token, site_url):

    headers = {"Authorization": f"Bearer {token}"}

    # Convert URL into Graph format
    # Example:
    # https://corpofficeapps.sharepoint.com/sites/Ops_Home/nationalops
    # =>
    # /sites/corpofficeapps.sharepoint.com:/sites/Ops_Home/nationalops

    from urllib.parse import urlparse

    parsed = urlparse(site_url)
    host = parsed.netloc
    path = parsed.path

    graph_url = f"https://graph.microsoft.com/v1.0/sites/{host}:{path}"

    response = requests.get(graph_url, headers=headers)
    response.raise_for_status()

    site_data = response.json()

    print("✅ Site Found:", site_data["displayName"])
    print("Site ID:", site_data["id"], "\n")

    return site_data["id"]


# ==============================
# SEARCH + READ FILE IN MEMORY
# ==============================

def search_and_read_file(token, site_id, filename):

    headers = {"Authorization": f"Bearer {token}"}

    # Search file
    search_url = f"https://graph.microsoft.com/v1.0/sites/{site_id}/drive/root/search(q='{filename}')"

    search_response = requests.get(search_url, headers=headers)
    search_response.raise_for_status()

    results = search_response.json().get("value", [])

    if not results:
        print("❌ File not found")
        return

    file_item = results[0]

    print("✅ File Found:", file_item["name"])
    print("Web URL:", file_item["webUrl"])

    # Read content directly (in memory)
    content_url = f"https://graph.microsoft.com/v1.0/sites/{site_id}/drive/items/{file_item['id']}/content"

    file_response = requests.get(content_url, headers=headers)
    file_response.raise_for_status()

    print("\n======= FILE DATA =======\n")

    try:
        print(file_response.text)
    except:
        print("⚠ Binary file detected (PDF/DOCX/etc). Cannot print as text.")


# ==============================
# MAIN
# ==============================

if __name__ == "__main__":

    token = get_access_token()

    site_id = get_site_id(token, SITE_URL)

    search_and_read_file(token, site_id, FILE_NAME)
