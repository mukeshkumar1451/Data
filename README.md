from urllib.parse import quote

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
