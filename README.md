import re
from bs4 import BeautifulSoup

ALL_CHANNELS = ["WHL", "RTL", "DTC", "CL1"]

def clean_html(raw_html: str) -> str:
    if not raw_html:
        return ""
    soup = BeautifulSoup(raw_html, "html.parser")
    return soup.get_text(separator=" ")

def detect_channels(description: str, acceptance_criteria: str) -> list:
    """
    Detect channels from Description + Acceptance Criteria.
    """

    combined_text = (
        clean_html(description) + " " +
        clean_html(acceptance_criteria)
    ).upper()

    found_channels = []

    for channel in ALL_CHANNELS:
        pattern = r"\b" + re.escape(channel) + r"\b"
        if re.search(pattern, combined_text):
            found_channels.append(channel)

    if not found_channels:
        print("\n⚠️  No channel mentioned in Description or Acceptance Criteria.")
        print("➡️  As per rule, selecting ALL channels: WHL, RTL, DTC, CL1\n")
        return ALL_CHANNELS

    print(f"\n✅ Channels detected: {found_channels}\n")
    return found_channels
