import re

ALL_CHANNELS = ["WHL", "RTL", "DTC", "CL1"]

def detect_channels_from_description(description: str) -> list:
    """
    Detect channel names from user story description.
    If none found, return all channels.
    """

    if not description:
        return ALL_CHANNELS

    description_upper = description.upper()

    found_channels = []

    for channel in ALL_CHANNELS:
        # match whole word like WHL, RTL, etc.
        pattern = r"\b" + re.escape(channel) + r"\b"
        if re.search(pattern, description_upper):
            found_channels.append(channel)

    return found_channels if found_channels else ALL_CHANNELS
