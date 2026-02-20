import requests
import io
import mammoth
import textract
import tempfile
import sys
from urllib.parse import unquote, urlparse

# ================= AZURE APP =================
TENANT_ID = ""
CLIENT_ID = ""
CLIENT_SECRET = ""

# ================= TEST SHAREPOINT URLS =================
TEST_URLS = [
    "https://corpofficeapps.sharepoint.com/sites/Ops_Home/nationalops/Shared Documents/Strategic Initiatives Team Folder/Cognizant UAT Results/Documents.doc",
    "https://corpofficeapps.sharepoint.com/sites/Ops_Home/Branch Site Audits/2012 Branch Site Audits/Branch Site Audit Checklist-Report - Santa Barbara 8.20.12.docx"
]


# =========================================================
# GRAPH HELPER (handles permission errors cleanly)
# =========================================================
def graph_get(url, token):
    headers = {"Authorization": f"Bearer {token}"}
    res = requests.get(url, headers=headers)

    if res.status_code < 400:
        return res

    try:
        err = res.json()["error"]
        code = err.get("code", "")
        msg = err.get("message", "")
    except:
        print(res.text)
        sys.exit()

    if res.status_code == 401:
        print("❌ Authentication failed — invalid client id/secret or token expired")
        sys.exit()

    if res.status_code == 403:
        print("\n🚫 ACCESS DENIED FROM MICROSOFT GRAPH")

        if "site" in msg.lower():
            print("Missing permission → Sites.Read.All")

        if "drive" in msg.lower() or "file" in msg.lower():
            print("Missing permission → Files.Read.All")

        print("\n👉 Fix in Azure Portal:")
        print("App Registration → API permissions → Add:")
        print("   Sites.Read.All (Application)")
        print("   Files.Read.All (Application)")
        print("Then click: GRANT ADMIN CONSENT\n")

        sys.exit()

    print(f"Graph error: {code} - {msg}")
    sys.exit()


# =========================================================
# AUTH
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


def validate_token(token):
    url = "https://graph.microsoft.com/v1.0/organization"
    graph_get(url, token)
    print("✅ Graph access verified")


# =========================================================
# SHAREPOINT URL PARSER
# =========================================================
def parse_sharepoint_url(url):
    parsed = urlparse(url)
    host = parsed.netloc

    parts = unquote(parsed.path).strip("/").split("/")

    sites_index = parts.index("sites")
    site_name = parts[sites_index + 1]

    remaining = parts[sites_index + 2:]

    # remove "Shared Documents" if present
    if remaining and remaining[0].lower() == "shared documents":
        remaining = remaining[1:]

    folders = remaining[:-1]
    filename = remaining[-1]

    return host, f"sites/{site_name}", folders, filename


# =========================================================
# GRAPH NAVIGATION
# =========================================================
def get_site_id(token, host, site_path):
    url = f"https://graph.microsoft.com/v1.0/sites/{host}:/{site_path}"
    res = graph_get(url, token)
    return res.json()["id"]


def get_drive_id(token, site_id):
    url = f"https://graph.microsoft.com/v1.0/sites/{site_id}/drive"
    res = graph_get(url, token)
    return res.json()["id"]


def get_item_by_path(token, drive_id, path_list):
    parent = "root"

    for part in path_list:
        url = f"https://graph.microsoft.com/v1.0/drives/{drive_id}/items/{parent}/children"
        res = graph_get(url, token)

        found = None
        for item in res.json()["value"]:
            if item["name"].lower() == part.lower():
                found = item["id"]
                break

        if not found:
            raise Exception(f"Not found → {part}")

        parent = found
        print(f"   📂 {part}")

    return parent


# =========================================================
# READ FILE CONTENT (NO PERMANENT DOWNLOAD)
# =========================================================
def read_file_content(token, drive_id, file_id):
    url = f"https://graph.microsoft.com/v1.0/drives/{drive_id}/items/{file_id}/content"
    res = graph_get(url, token)

    file_bytes = res.content

    # Try DOCX
    try:
        text = mammoth.extract_raw_text(io.BytesIO(file_bytes)).value
        if len(text.strip()) > 20:
            return text
    except:
        pass

    # Fallback DOC
    with tempfile.NamedTemporaryFile(suffix=".doc") as tmp:
        tmp.write(file_bytes)
        tmp.flush()
        return textract.process(tmp.name).decode("utf-8", errors="ignore")


# =========================================================
# TEST MULTIPLE URL ACCESS
# =========================================================
def test_multiple_paths(token):

    for url in TEST_URLS:
        print("\n========================================")
        print("Testing:", url)

        try:
            host, site_path, folders, file = parse_sharepoint_url(url)

            site_id = get_site_id(token, host, site_path)
            print("   ✔ Site accessible")

            drive_id = get_drive_id(token, site_id)
            print("   ✔ Drive accessible")

            file_id = get_item_by_path(token, drive_id, folders + [file])

            content = read_file_content(token, drive_id, file_id)

            print("\n   ✅ FILE READ SUCCESS — PREVIEW:\n")
            print(content[:600])

        except SystemExit:
            print("   ❌ Permission issue")

        except Exception as e:
            print("   ⚠ Path not reachable:", str(e))


# =========================================================
# MAIN
# =========================================================
if __name__ == "__main__":

    token = get_access_token()
    validate_token(token)
    test_multiple_paths(token)
