import requests
import traceback
from collections import defaultdict

TENANT_ID = ''
CLIENT_ID = ''
CLIENT_SECRET = ''

SITE_HOST = "corpoffice.sharepoint.com"
SITE_PATH = "/sites/Ops_Home"

# ================= SAFE PRINT =================
def print_response(res, step):
    print("\n=========== RESPONSE DEBUG ===========")
    print("STEP:", step)
    print("STATUS:", res.status_code)
    print("RAW:", res.text[:1000])
    print("======================================\n")

# ================= TOKEN =================
def get_access_token():
    try:
        url = f"https://login.microsoftonline.com/{TENANT_ID}/oauth2/v2.0/token"

        data = {
            "client_id": CLIENT_ID,
            "scope": "https://graph.microsoft.com/.default",
            "client_secret": CLIENT_SECRET,
            "grant_type": "client_credentials"
        }

        res = requests.post(url, data=data)

        if res.status_code != 200:
            print_response(res, "TOKEN")
            raise Exception("Token failed")

        print("✅ Token acquired")
        return res.json()["access_token"]

    except Exception:
        traceback.print_exc()
        exit()


# ================= SITE =================
def get_site_id(token):
    try:
        headers = {"Authorization": f"Bearer {token}"}
        url = f"https://graph.microsoft.com/v1.0/sites/{SITE_HOST}:{SITE_PATH}"

        res = requests.get(url, headers=headers)

        if res.status_code != 200:
            print_response(res, "SITE ACCESS")
            raise Exception("Site access failed")

        print("✅ Site accessible")
        return res.json()["id"]

    except Exception:
        traceback.print_exc()
        exit()


# ================= DRIVE =================
def get_drive_id(token, site_id):
    try:
        headers = {"Authorization": f"Bearer {token}"}
        url = f"https://graph.microsoft.com/v1.0/sites/{site_id}/drives"

        res = requests.get(url, headers=headers)

        if res.status_code != 200:
            print_response(res, "DRIVES")
            raise Exception("Drive fetch failed")

        for d in res.json().get("value", []):
            if d["name"] in ["Documents", "Shared Documents"]:
                print("✅ Found library:", d["name"])
                return d["id"]

        raise Exception("No document library found")

    except Exception:
        traceback.print_exc()
        exit()


# ================= SCAN =================
file_counts = defaultdict(int)

def scan(token, drive_id, path="root"):
    try:
        headers = {"Authorization": f"Bearer {token}"}

        if path == "root":
            url = f"https://graph.microsoft.com/v1.0/drives/{drive_id}/root/children"
        else:
            url = f"https://graph.microsoft.com/v1.0/drives/{drive_id}/root:/{path}:/children"

        res = requests.get(url, headers=headers)

        if res.status_code != 200:
            print_response(res, f"SCAN {path}")
            return

        items = res.json().get("value", [])

        for item in items:
            if "file" in item:
                ext = item["name"].split(".")[-1].lower() if "." in item["name"] else "noext"
                file_counts[ext] += 1

            if "folder" in item:
                parent = item["parentReference"]["path"].split("root:")[-1].strip("/")
                new_path = f"{parent}/{item['name']}" if parent else item["name"]
                scan(token, drive_id, new_path)

    except Exception:
        traceback.print_exc()


# ================= MAIN =================
if __name__ == "__main__":

    token = get_access_token()
    site_id = get_site_id(token)
    drive_id = get_drive_id(token, site_id)

    print("\nScanning...\n")
    scan(token, drive_id)

    print("\n===== FILE COUNT =====")
    total = 0
    for ext, count in file_counts.items():
        print(ext, ":", count)
        total += count
    print("TOTAL:", total)
