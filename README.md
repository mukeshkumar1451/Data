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

# Folder extracted from your URL
START_FOLDER = "Documents/Strategic Initiatives Team Folder/Cognizant UAT Results"


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
        raise Exception("Token failed: " + res.text)

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

    # Full server relative path
    full_path = f"{SITE_PATH}/{folder_relative_url}"
    encoded = quote(full_path)

    url = f"{SITE}{SITE_PATH}/_api/web/GetFolderByServerRelativePath(decodedurl='{encoded}')?$expand=Folders,Files"

    res = requests.get(url, headers=headers)

    if res.status_code != 200:
        print("❌ Cannot access:", folder_relative_url)
        print(res.text)
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


    if folder_relative_url in visited:
        return
    visited.add(folder_relative_url)

    encoded_path = quote(f"{SITE_PATH}/{folder_relative_url}")

    headers = {
        "Authorization": f"Bearer {token}",
        "Accept": "application/json;odata=verbose"
    }

    url = f"{SITE}{SITE_PATH}/_api/web/GetFolderByServerRelativeUrl('{encoded_path}')?$expand=Folders,Files"

    res = requests.get(url, headers=headers)

    if res.status_code != 200:
        print("❌ Cannot access:", folder_relative_url)
        print(res.text)
        return

    data = res.json()["d"]

    # ---------- FILES ----------
    for file in data["Files"]["results"]:
        name = file["Name"]
        ext = name.split(".")[-1].lower() if "." in name else "noext"
        file_counts[ext] += 1

    # ---------- SUBFOLDERS ----------
    for folder in data["Folders"]["results"]:
        name = folder["Name"]

        # Skip system folder
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
