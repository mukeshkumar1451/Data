def validate_token(token):
    url = "https://graph.microsoft.com/v1.0/organization"
    headers = {"Authorization": f"Bearer {token}"}
    res = requests.get(url, headers=headers)

    print("Status:", res.status_code)
    print(res.text)
