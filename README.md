import requests
import io
import mammoth
import textract
import tempfile

SITE_HOST = "corpofficeapps.sharepoint.com"
SITE_PATH = "sites/Ops_Home/nationalops"
TARGET_FILE = "Documents.doc"

# ---------------- FIND SITE ----------------
def get_site_id(token):
    headers = {"Authorization": f"Bearer {token}"}
    url = f"https://graph.microsoft.com/v1.0/sites/{SITE_HOST}:/{SITE_PATH}"
    res = requests.get(url, headers=headers)
    res.raise_for_status()
    return res.json()["id"]

# ---------------- GET DRIVE ----------------
def get_drive_id(token, site_id):
    headers = {"Authorization": f"Bearer {token}"}
    url = f"https://graph.microsoft.com/v1.0/sites/{site_id}/drive"
    res = requests.get(url, headers=headers)
    res.raise_for_status()
    return res.json()["id"]

# ---------------- WALK FOLDERS ----------------
def get_item_by_path(token, drive_id, path_list):
    headers = {"Authorization": f"Bearer {token}"}

    parent = "root"

    for part in path_list:
        url = f"https://graph.microsoft.com/v1.0/drives/{drive_id}/items/{parent}/children"
        res = requests.get(url, headers=headers)
        res.raise_for_status()

        found = None
        for item in res.json()["value"]:
            if item["name"].lower() == part.lower():
                found = item["id"]
                break

        if not found:
            raise Exception(f"Folder/File not found: {part}")

        parent = found

    return parent

# ---------------- READ CONTENT ----------------
def read_file_content(token, drive_id, file_id):
    headers = {"Authorization": f"Bearer {token}"}
    url = f"https://graph.microsoft.com/v1.0/drives/{drive_id}/items/{file_id}/content"
    res = requests.get(url, headers=headers)
    res.raise_for_status()

    data = res.content
    print("✅ File stream received")

    # Try DOCX parser
    try:
        text = mammoth.extract_raw_text(io.BytesIO(data)).value
        if len(text.strip()) > 20:
            return text
    except:
        pass

    # Fallback DOC
    with tempfile.NamedTemporaryFile(suffix=".doc") as tmp:
        tmp.write(data)
        tmp.flush()
        return textract.process(tmp.name).decode("utf-8", errors="ignore")

# ---------------- MAIN ----------------
def read_sharepoint_doc(token):

    site_id = get_site_id(token)
    print("Site resolved")

    drive_id = get_drive_id(token, site_id)
    print("Drive resolved")

    path = [
        "Strategic Initiatives Team Folder",
        "Cognizant UAT Results",
        TARGET_FILE
    ]

    file_id = get_item_by_path(token, drive_id, path)
    print("File located")

    content = read_file_content(token, drive_id, file_id)

    print("\n=========== DOCUMENT TEXT ===========\n")
    print(content)
