ALL_CHANNELS = ["WHL", "RTL", "DTC", "CL1"]

CHANNEL_KEYWORDS = {
    "RTL": ["RTL", "RETAIL"],
    "WHL": ["WHL", "WHOLESALE", "BROKER"],
    "DTC": ["DTC", "DIRECT TO CUSTOMER"],
    "CL1": ["CL1", "CORRESPONDENT"]
}


def detect_channels(acceptance_criteria: str) -> list:
    print("\n🔍 Detecting channels from Acceptance Criteria...\n")

    text = acceptance_criteria.upper()

    negation_words = [
        "DO NOT", "DOES NOT", "NOT ", "NO ",
        "WITHOUT", "SHOULD NOT", "CANNOT",
        "NEVER", "EXCEPT"
    ]

    found_channels = []
    lines = text.split(". ")

    for channel, keywords in CHANNEL_KEYWORDS.items():
        for word in keywords:
            for line in lines:
                if any(neg in line for neg in negation_words):
                    continue
                if word in line:
                    found_channels.append(channel)
                    break
            else:
                continue
            break

    if not found_channels:
        print("⚠️ No channel mentioned → Using ALL channels")
        return ALL_CHANNELS

    print(f"✅ Channels detected: {found_channels}")
    return found_channels
