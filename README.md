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
    return soup.get_text(separator=" ")

def detect_channels(description: str, acceptance_criteria: str) -> list:
    combined_text = (
        clean_html(description) + " " +
        clean_html(acceptance_criteria)
    ).upper()

    found_channels = []

    for channel, keywords in CHANNEL_KEYWORDS.items():
        for word in keywords:
            pattern = r"\b" + re.escape(word) + r"\b"
            if re.search(pattern, combined_text):
                found_channels.append(channel)
                break

    if not found_channels:
        print("\n⚠️  No channel keywords found in Description or AC.")
        print("➡️  Selecting ALL channels: WHL, RTL, DTC, CL1\n")
        return ALL_CHANNELS

    print(f"\n✅ Channels detected from AC/Description: {found_channels}\n")
    return found_channels
