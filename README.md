import requests
import jwt

# ==============================
# CONFIGURATION (SET YOUR VALUES)
# ==============================

TENANT_ID = "YOUR_TENANT_ID"
CLIENT_ID = "YOUR_CLIENT_ID"
CLIENT_SECRET = "YOUR_CLIENT_SECRET"

# Subsite ID for:
# https://corpofficeapps.sharepoint.com/sites/Ops_Home/nationalops
SITE_ID = "corpofficeapps.sharepoint.com,bf088619-af20-4ee0-aed9-f59eadef4cc4,985d34fb-093d-449c-a958-0865ce7aac7d"


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
# GET ROOT FOLDERS
# ==============================

def get_root_folders(token, site_id):
    url = f"https://graph.microsoft.com/v1.0/sites/{site_id}/drive/root/children"

    headers = {
        "Authorization": f"Bearer {token}"
    }

    response = requests.get(url, headers=headers)
    response.raise_for_status()

    items = response.json().get("value", [])

    print("======= FOLDERS IN DEFAULT DOCUMENT LIBRARY =======\n")

    for item in items:
        if "folder" in item:
            print("📁", item["name"])

    print("\nDone.")


# ==============================
# MAIN
# ==============================

if __name__ == "__main__":

    token = get_access_token()
    get_root_folders(token, SITE_ID)
