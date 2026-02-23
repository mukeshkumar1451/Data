import jwt

def inspect_token(token):
    decoded = jwt.decode(token, options={"verify_signature": False})
    print("\n======= TOKEN CLAIMS =======\n")
    for key, value in decoded.items():
        print(key, ":", value)
