# channel_detector.py

import logging
import re

logger = logging.getLogger(__name__)

ALL_CHANNELS = ["WHL", "RTL", "DTC", "CL1"]

CHANNEL_KEYWORDS = {
    "RTL": ["RTL", "RETAIL", "RET"],
    "WHL": ["WHL", "WHOLESALE"],
    "DTC": ["DTC", "DIRECT TO CUSTOMER"],
    "CL1": ["CL1", "CORRESPONDENT"]
}

NEGATION_PATTERNS = [
    r"WHL.*DOES NOT",
    r"WHL.*NOT APPLICABLE",
    r"WHL.*NOT AVAILABLE",
    r"WHL.*EXCEPT",
]


def detect_channels(text: str) -> list:

    logger.info("\n🔎 Detecting channels from Acceptance Criteria...\n")

    if not text:
        return ALL_CHANNELS

    text = text.upper()

    detected = set()
    excluded = set()

    # -------------------------------------------
    # Step 1: Detect exclusions (negation)
    # -------------------------------------------
    for channel in CHANNEL_KEYWORDS:

        if re.search(fr"{channel}.*DOES NOT", text):
            excluded.add(channel)

        if re.search(fr"{channel}.*NOT APPLICABLE", text):
            excluded.add(channel)

        if re.search(fr"{channel}.*NOT AVAILABLE", text):
            excluded.add(channel)

    logger.info(f"🚫 Excluded channels: {excluded}")

    # -------------------------------------------
    # Step 2: Detect channels
    # -------------------------------------------
    for channel, words in CHANNEL_KEYWORDS.items():
        for w in words:
            if w in text:
                detected.add(channel)

    logger.info(f"🦬 Raw detected channels: {detected}")

    # -------------------------------------------
    # Step 3: Remove excluded
    # -------------------------------------------
    detected = detected - excluded

    logger.info(f"✔ After removing exclusions: {detected}")

    # -------------------------------------------
    # Step 4: Business rules
    # -------------------------------------------
    final_channels = set()

    if not detected:
        logger.info("⚠️ No channel mentioned → Using ALL channels")
        return ALL_CHANNELS

    if "RTL" in detected:
        final_channels.update(["RTL", "DTC"])

    if "WHL" in detected:
        final_channels.update(["WHL", "CL1"])

    if "DTC" in detected:
        final_channels.add("DTC")

    if "CL1" in detected:
        final_channels.add("CL1")

    logger.info(f"✅ Final channels after rule mapping: {final_channels}\n")

    return list(final_channels)
