import requests
from collections import defaultdict

TENANT_ID = ''
CLIENT_ID = ''
CLIENT_SECRET = ''

SITE_HOST = "corpoffice.sharepoint.com"
SITE_PATH = "/sites/Ops_Home"

START_FOLDER = "Shared Documents"   # change if needed


# ================= ERROR EXPLAINER =================
def explain_error(res, step=""):
    try:
        data = res.json()
        code = data.get("error", {}).get("code", "")
        msg = data.get("error", {}).get("message", "")
    except:
        code = "unknown"
        msg = res.text

    print("\n=========== GRAPH ERROR ===========")
    print("STEP :", step)
    print("HTTP :", res.status_code)
    print("CODE :", code)
    print("MSG  :", msg)
    print("===================================\n")

    if res.status_code == 401:
        print("🔐 Authentication failed → wrong tenant/client/secret")

    elif res.status_code == 403:
        if "Access denied" in msg:
            print("🚫 Sites.Selected permission NOT granted to site")
        else:
            print("🚫 Permission denied")

    elif res.status_code == 404:
        print("📂 Site or folder path incorrect")

    print()


# ================= TOKEN =================
def get_access_token():
    url = f"https://login.microsoftonline.com/{TENANT_ID}/oauth2/v2.0/token"

    data = {
        "client_id": CLIENT_ID,
        "scope": "https://graph.microsoft.com/.default",
        "client_secret": CLIENT_SECRET,
        "grant_type": "client_credentials"
    }

    res = requests.post(url, data=data)

    if res.status_code != 200:
        explain_error(res, "TOKEN")
        exit()

    print("✅ Token acquired")
    return res.json()["access_token"]


# ================= SITE =================
def get_site_id(token):
    headers = {"Authorization": f"Bearer {token}"}

    url = f"https://graph.microsoft.com/v1.0/sites/{SITE_HOST}:{SITE_PATH}"

    res = requests.get(url, headers=headers)

    if res.status_code != 200:
        explain_error(res, "SITE ACCESS")
        exit()

    print("✅ Site access granted")
    return res.json()["id"]


# ================= DRIVE =================
def get_drive_id(token, site_id):
    headers = {"Authorization": f"Bearer {token}"}
    url = f"https://graph.microsoft.com/v1.0/sites/{site_id}/drives"

    res = requests.get(url, headers=headers)

    if res.status_code != 200:
        explain_error(res, "GET DRIVES")
        exit()

    for d in res.json()["value"]:
        if d["name"] in ["Documents", "Shared Documents"]:
            print("✅ Found document library:", d["name"])
            return d["id"]

    exit("❌ No document library found")


# ================= COUNTER =================
file_counts = defaultdict(int)


# ================= RECURSIVE SCAN =================
def scan_folder(token, drive_id, folder_path="root"):
    headers = {"Authorization": f"Bearer {token}"}

    if folder_path == "root":
        url = f"https://graph.microsoft.com/v1.0/drives/{drive_id}/root/children"
    else:
        url = f"https://graph.microsoft.com/v1.0/drives/{drive_id}/root:/{folder_path}:/children"

    res = requests.get(url, headers=headers)

    if res.status_code != 200:
        explain_error(res, f"SCAN {folder_path}")
        return

    items = res.json().get("value", [])

    for item in items:

        # FILE
        if "file" in item:
            name = item["name"]
            ext = name.split(".")[-1].lower() if "." in name else "noext"
            file_counts[ext] += 1

        # FOLDER
        if "folder" in item:
            new_path = item["parentReference"]["path"].split("root:")[-1].strip("/")
            new_path = f"{new_path}/{item['name']}" if new_path else item["name"]
            scan_folder(token, drive_id, new_path)


# ================= MAIN =================
if __name__ == "__main__":

    token = get_access_token()
    site_id = get_site_id(token)
    drive_id = get_drive_id(token, site_id)

    print("\nScanning SharePoint via Graph...\n")
    scan_folder(token, drive_id)

    print("\n========= FILE TYPE COUNT =========")
    total = 0
    for ext, count in sorted(file_counts.items()):
        print(f"{ext.upper():10} : {count}")
        total += count

    print("-----------------------------------")
    print("TOTAL FILES :", total)
