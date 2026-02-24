import requests
import jwt
from urllib.parse import urlparse

# ================= AZURE APP =================


# ================= SITES TO VERIFY =================
SITES_TO_TEST = [
    "https://corpofficeapps.sharepoint.com/sites/Ops_Home",
    "https://corpofficeapps.sharepoint.com/sites/nationalops"
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
# INSPECT TOKEN
# =========================================================
def inspect_token(token):
    decoded = jwt.decode(token, options={"verify_signature": False})
    print("\n======= TOKEN CLAIMS =======\n")
    print("aud:", decoded.get("aud"))
    print("roles:", decoded.get("roles"))
    print("appid:", decoded.get("appid"))
    print("tid:", decoded.get("tid"))


# =========================================================
# VERIFY SITE ACCESS
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
    print("Graph URL:", graph_url)
    print("Status:", res.status_code)
    print("Response:", res.text)

    if res.status_code == 200:
        print("✔ ACCESS GRANTED")
    elif res.status_code == 403:
        print("❌ ACCESS NOT GRANTED (Sites.Selected not assigned)")
    elif res.status_code == 401:
        print("🔐 401 Unauthorized → Token/Permission issue")
    else:
        print("⚠ Unexpected issue")


# =========================================================
# MAIN
# =========================================================
if __name__ == "__main__":

    token = get_access_token()
    inspect_token(token)

    print("\nChecking Sites.Selected permissions...\n")

    for site in SITES_TO_TEST:
        verify_site_access(token, site)
-----------------------------------------------------
======= TOKEN CLAIMS =======

aud: https://graph.microsoft.com
roles: ['Sites.Selected']
appid: c23ec558-0644-496c-9ff0-b2475e086766
tid: 68c05827-e75e-4060-ae30-f37c77fc1f70

Checking Sites.Selected permissions...


-------------------------------------------
Testing: https://corpofficeapps.sharepoint.com/sites/Ops_Home
Graph URL: https://graph.microsoft.com/v1.0/sites/corpofficeapps.sharepoint.com:/sites/Ops_Home
Status: 200
Response: {"@odata.context":"https://graph.microsoft.com/v1.0/$metadata#sites/$entity","createdDateTime":"2018-09-19T19:05:36.62Z","description":"","id":"corpofficeapps.sharepoint.com,bf088619-af20-4ee0-aed9-f59eadef4cc4,5eb9ca1c-4861-4ccb-9370-5c509bfd7680","lastModifiedDateTime":"2026-02-24T18:11:28Z","name":"Ops_Home","webUrl":"https://corpofficeapps.sharepoint.com/sites/Ops_Home","displayName":"Operations Extranet Home","root":{},"siteCollection":{"hostname":"corpofficeapps.sharepoint.com"}}
✔ ACCESS GRANTED

-------------------------------------------
Testing: https://corpofficeapps.sharepoint.com/sites/nationalops
Graph URL: https://graph.microsoft.com/v1.0/sites/corpofficeapps.sharepoint.com:/sites/nationalops
Status: 404
Response: {"error":{"code":"itemNotFound","message":"Requested site could not be found","innerError":{"date":"2026-02-24T19:48:00","request-id":"50169a2f-c604-483d-a424-1d26468f27d4","client-request-id":"50169a2f-c604-483d-a424-1d26468f27d4"}}}
⚠ Unexpected issue
(.venv) PS C:\Users\h84609n\Desktop\sharepointconnection> 
