# channel_detector.py
ALL_CHANNELS = ["WHL", "RTL", "DTC", "CL1"]

import logging

logger = logging.getLogger(__name__)

CHANNEL_KEYWORDS = {
    "RTL": ["RTL", "RETAIL","RET"],
    "WHL": ["WHL", "WHOLESALE"],
    "DTC": ["DTC", "DIRECT TO CUSTOMER"],
    "CL1": ["CL1", "CORRESPONDENT"]
}


def detect_channels(acceptance_criteria: str) -> list:
    logger.info("\n🔎 Detecting channels from Acceptance Criteria...\n")

    text = acceptance_criteria.upper()

    detected = set()

    # -------------------------------------------
    # Step 1: Basic keyword detection
    # -------------------------------------------
    for channel, words in CHANNEL_KEYWORDS.items():
        for w in words:
            if w in text:
                detected.add(channel)

    logger.info(f"🦬 Raw detected channels: {detected}")

    # -------------------------------------------
    # Step 2: Business rules mapping
    # -------------------------------------------
    final_channels = set()

    if not detected:
        logger.info("⚠️ No channel mentioned → Using ALL channels")
        return ALL_CHANNELS

    # Rule 1
    if "RTL" in detected:
        final_channels.update(["RTL", "DTC"])

    if "WHL" in detected:
        final_channels.update(["WHL", "CL1"])

    # Rule 2 (if explicitly mentioned)
    if "DTC" in detected:
        final_channels.add("DTC")

    if "CL1" in detected:
        final_channels.add("CL1")

    logger.info(f"✅ Final channels after rule mapping: {final_channels}\n")

    return list(final_channels)
