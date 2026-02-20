import requests
import io
import mammoth
import tempfile
import textract

TENANT_ID = ""
CLIENT_ID = ""
CLIENT_SECRET = ""

SITE_HOST = "corpofficeapps.sharepoint.com"
SITE_NAME = "Ops_Home"

FILE_PATH = "nationalops/Shared Documents/Strategic Initiatives Team Folder/Cognizant UAT Results/Documents.doc"


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
    res.raise_for_status()
    return res.json()["access_token"]


# ================= READ FILE IN MEMORY =================
def read_sharepoint_doc(token):
    headers = {"Authorization": f"Bearer {token}"}

    encoded_path = FILE_PATH.replace(" ", "%20")

    url = f"https://graph.microsoft.com/v1.0/sites/{SITE_HOST}:/sites/{SITE_NAME}:/drive/root:/{encoded_path}:/content"

    res = requests.get(url, headers=headers)
    res.raise_for_status()

    file_bytes = res.content
    print("✅ File fetched from SharePoint memory")

    # ---- Try DOCX parser first ----
    try:
        result = mammoth.extract_raw_text(io.BytesIO(file_bytes))
        text = result.value.strip()

        if len(text) > 20:
            return text
    except:
        pass

    # ---- Fallback for old .doc ----
    with tempfile.NamedTemporaryFile(delete=True, suffix=".doc") as tmp:
        tmp.write(file_bytes)
        tmp.flush()
        text = textract.process(tmp.name).decode("utf-8", errors="ignore")
        return text


# ================= MAIN =================
if __name__ == "__main__":
    token = get_access_token()
    print("Token OK")

    content = read_sharepoint_doc(token)

    print("\n=========== DOCUMENT CONTENT ===========\n")
    print(content)
