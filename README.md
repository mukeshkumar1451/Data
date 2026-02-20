import requests



SITE_HOST = "corpoffice.sharepoint.com"
SITE_PATH = "https://corpofficeapps.sharepoint.com/sites/Ops_Home/nationalops/Shared%20Documents/Strategic%20Initiatives%20Team%20Folder/Cognizant%20UAT%20Results"  # <<< PATH TO SEARCH IN



TARGET_FILE_NAME="Documents.doc"
# ================= TOKEN =================
def get_access_token():
    token_url = f"https://login.microsoftonline.com/{TENANT_ID}/oauth2/v2.0/token"

    data = {
        "client_id": CLIENT_ID,
        "scope": "https://graph.microsoft.com/.default",
        "client_secret": CLIENT_SECRET,
        "grant_type": "client_credentials",
    }

    res = requests.post(token_url, data=data)
    return res.json()["access_token"]


# ================= SITE =================
def get_site_id(token):
    headers = {"Authorization": f"Bearer {token}"}
    url = f"https://{SITE_HOST}:{SITE_PATH}"
    res = requests.get(url, headers=headers)

    if res.status_code != 200:
        print(res.text)
        exit("❌ Site access denied")

    return res.json()["id"]


# ================= DRIVE =================
def get_documents_drive(token, site_id):
    headers = {"Authorization": f"Bearer {token}"}
    url = f"https://graph.microsoft.com/v1.0/sites/{site_id}/drives"
    res = requests.get(url, headers=headers)

    for drive in res.json()["value"]:
        if drive["name"] == "Documents":
            return drive["id"]

    exit("❌ Documents library not found")


# ================= FIND FILE =================
def find_file(token, drive_id, filename):
    headers = {"Authorization": f"Bearer {token}"}
    url = f"https://graph.microsoft.com/v1.0/drives/{drive_id}/root/search(q='{filename}')"

    res = requests.get(url, headers=headers)

    if res.status_code != 200:
        print(res.text)
        exit("❌ Search failed")

    results = res.json()["value"]

    for f in results:
        if f["name"].lower() == filename.lower():
            print("✅ Found file:", f["name"])
            return f["id"]

    exit("❌ File not found")


# ================= READ FILE =================
def read_file(token, drive_id, file_id):
    headers = {"Authorization": f"Bearer {token}"}
    url = f"https://graph.microsoft.com/v1.0/drives/{drive_id}/items/{file_id}/content"

    res = requests.get(url, headers=headers)

    if res.status_code == 200:
        print("✅ File content read successfully")
        return res.text
    else:
        print(res.text)
        exit("❌ Failed to read file")


# ================= MAIN =================
if __name__ == "__main__":

    token = get_access_token()
    print("Token OK")

    site_id = get_site_id(token)
    print("Site OK")

    drive_id = get_documents_drive(token, site_id)
    print("Drive OK")

    file_id = find_file(token, drive_id, TARGET_FILE_NAME)

    file_content = read_file(token, drive_id, file_id)
    print("File Content:")
    print(file_content)
