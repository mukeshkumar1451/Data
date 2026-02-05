import re
from bs4 import BeautifulSoup

ALL_CHANNELS = ["WHL", "RTL", "DTC", "CL1"]

CHANNEL_KEYWORDS = {
    "RTL": ["RTL", "RETAIL", "RETAIL CHANNEL"],
    "WHL": ["WHL", "WHOLESALE", "WHOLESALE BROKER", "BROKER CHANNEL"],
    "DTC": ["DTC", "DIRECT TO CUSTOMER"],
    "CL1": ["CL1", "CORRESPONDENT", "CORRESPONDENT CHANNEL"]
}

def clean_html(raw_html: str) -> str:
    if not raw_html:
        return ""
    soup = BeautifulSoup(raw_html, "html.parser")
    text = soup.get_text(separator=" ")
    return " ".join(text.split()).lower()

def detect_channels( acceptance_criteria: str) -> list:
    print("\n[DEBUG] Acceptance Criteria received:")
    print(acceptance_criteria)
    combined_text = clean_html(acceptance_criteria)

    negation_words = [
        "do not", "does not", "did not", "not ", "no ", "without", "should not", "cannot", "can't", "won't", "never", "ignores", "ignore", "except"
    ]

    found_channels = []
    lines = combined_text.split(". ")  # Split by sentences (simple)

    for channel, keywords in CHANNEL_KEYWORDS.items():
        for word in keywords:
            for line in lines:
                if any(neg in line for neg in negation_words):
                    continue
                if word.lower() in line:
                    found_channels.append(channel)
                    break
            else:
                continue
            break

    if not found_channels:
        print("\n⚠️  No channel keywords found in Acceptence criteria.")
        print("➡️  Selecting ALL channels: WHL, RTL, DTC, CL1\n")
        return ALL_CHANNELS

    print(f"\n✅ Channels detected from AC/Description: {found_channels}\n")
    return found_channels

