import requests
import sys
from urllib.parse import urlparse

# ================= AZURE APP =================
TENANT_ID = ""
CLIENT_ID = ""
CLIENT_SECRET = ""

# ================= SITES TO VERIFY =================
SITES_TO_TEST = [
    "https://corpofficeapps.sharepoint.com/sites/Ops_Home",
    "https://corpofficeapps.sharepoint.com/sites/AnotherSite"
]


# =========================================================
# GET ACCESS TOKEN
# =========================================================
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

    print("✅ Token acquired")
    return res.json()["access_token"]


# =========================================================
# VERIFY SINGLE SITE ACCESS
# =========================================================
def verify_site_access(token, site_url):

    parsed = urlparse(site_url)
    host = parsed.netloc
    site_path = parsed.path.strip("/")

    graph_url = f"https://graph.microsoft.com/v1.0/sites/{host}:/{site_path}"

    headers = {"Authorization": f"Bearer {token}"}
    res = requests.get(graph_url, headers=headers)

    print("\n-------------------------------------------")
    print("Testing:", site_url)

    if res.status_code == 200:
        print("✔ ACCESS GRANTED")
        site_id = res.json()["id"]
        print("Site ID:", site_id)
        return True

    if res.status_code == 403:
        print("❌ ACCESS NOT GRANTED (Sites.Selected not assigned to this site)")
        return False

    if res.status_code == 401:
        print("🔐 Authentication issue (check client secret / tenant)")
        return False

    print("⚠ Unexpected error:", res.status_code)
    print(res.text)
    return False


# =========================================================
# MAIN
# =========================================================
if __name__ == "__main__":

    token = get_access_token()

    print("\nChecking Sites.Selected permissions...\n")

    for site in SITES_TO_TEST:
        verify_site_access(token, site)
