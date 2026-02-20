import requests
from collections import defaultdict
from urllib.parse import quote

# ================= CREDENTIALS =================
TENANT_ID = ''
CLIENT_ID = ''
CLIENT_SECRET = ''

# ================= SHAREPOINT CONFIG =================
SITE = "https://corpofficeapps.sharepoint.com"
SITE_PATH = "/sites/Ops_Home/nationalops"

START_FOLDER = "Shared Documents/Strategic Initiatives Team Folder/Cognizant UAT Results"

# ================= ERROR EXPLAINER =================
def explain_error(res, path=""):
    try:
        data = res.json()
        message = data.get("error", {}).get("message", "")
        code = data.get("error", {}).get("code", "")
    except:
        message = res.text
        code = "Unknown"

    print("\n================ ERROR =================")
    print("Folder :", path)
    print("Status :", res.status_code)
    print("Code   :", code)
    print("Msg    :", message)
    print("========================================")

    if res.status_code == 401:
        print("🔐 Authentication failed → check client id / secret / tenant")

    elif res.status_code == 403:
        if "Unsupported app only token" in message:
            print("🏢 Tenant blocks SharePoint App-Only (PnP not allowed)")
        elif "Access denied" in message:
            print("🚫 App has no permission to this site/folder")
        else:
            print("🚫 Permission issue")

    elif res.status_code == 404:
        print("📂 Folder path wrong")

    print()


# ================= TOKEN =================
def get_token():
    token_url = f"https://login.microsoftonline.com/{TENANT_ID}/oauth2/v2.0/token"

    data = {
        "client_id": CLIENT_ID,
        "client_secret": CLIENT_SECRET,
        "grant_type": "client_credentials",
        "scope": f"{SITE}/.default"
    }

    res = requests.post(token_url, data=data)

    if res.status_code != 200:
        explain_error(res)
        raise Exception("Token failed")

    return res.json()["access_token"]


# ================= GLOBAL COUNTER =================
file_counts = defaultdict(int)
visited = set()


# ================= SCAN FUNCTION =================
def scan_folder(token, folder_relative_url):

    if folder_relative_url in visited:
        return
    visited.add(folder_relative_url)

    headers = {
        "Authorization": f"Bearer {token}",
        "Accept": "application/json;odata=nometadata"
    }

    full_path = f"{SITE_PATH}/{folder_relative_url}"
    encoded = quote(full_path)

    url = f"{SITE}{SITE_PATH}/_api/web/GetFolderByServerRelativePath(decodedurl='{encoded}')?$expand=Folders,Files"

    res = requests.get(url, headers=headers)

    if res.status_code != 200:
        explain_error(res, folder_relative_url)
        return

    data = res.json()

    # -------- FILES --------
    for file in data.get("Files", []):
        name = file["Name"]
        ext = name.split(".")[-1].lower() if "." in name else "noext"
        file_counts[ext] += 1

    # -------- SUBFOLDERS --------
    for folder in data.get("Folders", []):
        name = folder["Name"]

        if name.lower() == "forms":
            continue

        new_path = f"{folder_relative_url}/{name}"
        scan_folder(token, new_path)


# ================= MAIN =================
if __name__ == "__main__":

    print("Connecting to SharePoint...")
    token = get_token()
    print("✅ Connected\n")

    print("Scanning folders...\n")
    scan_folder(token, START_FOLDER)

    print("\n========== FILE TYPE COUNT ==========")
    total = 0

    for ext, count in sorted(file_counts.items()):
        print(f"{ext.upper():10} : {count}")
        total += count

    print("-------------------------------------")
    print("TOTAL FILES :", total)
