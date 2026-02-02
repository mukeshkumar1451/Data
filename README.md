import re
from bs4 import BeautifulSoup

ALL_CHANNELS = ["WHL", "RTL", "DTC", "CL1"]

def clean_html(raw_html: str) -> str:
    """Convert ADO HTML description into plain text."""
    if not raw_html:
        return ""
    soup = BeautifulSoup(raw_html, "html.parser")
    return soup.get_text(separator=" ")

def detect_channels_from_description(description: str) -> list:
    """
    Detect channel names from cleaned user story description.
    If none found, return all channels.
    """

    clean_text = clean_html(description).upper()

    found_channels = []

    for channel in ALL_CHANNELS:
        pattern = r"\b" + re.escape(channel) + r"\b"
        if re.search(pattern, clean_text):
            found_channels.append(channel)

    return found_channels if found_channels else ALL_CHANNELS
