def verify_site_access(token, site_url):

    from urllib.parse import urlparse

    parsed = urlparse(site_url)

    host = parsed.netloc  # corpofficeapps.sharepoint.com
    site_path = parsed.path.strip("/")  # sites/Ops_Home

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
        print("🔐 Token valid but Graph rejected it (check admin consent)")
    else:
        print("⚠ Unexpected issue")
