# -*- coding: utf-8 -*-
# utils/channel_detector.py
import re
import logging

logger = logging.getLogger(__name__)

ALL_CHANNELS = ["RTL", "WHL", "DTC", "CL1"]

CHANNEL_MAP = {
    "RTL": ["RTL", "RET", "RETAIL"],
    "WHL": ["WHL", "WHOLESALE"],
    "DTC": ["DTC", "DIRECT TO CUSTOMER"],
    "CL1": ["CL1", "CORRESPONDENT"]
}


def sentence_contains_channel(sentence, keywords):
    return any(k in sentence for k in keywords)


def detect_channels(ac_text: str) -> list:
    logger.info("Intelligent channel detection started...")

    text = ac_text.upper()
    sentences = re.split(r'[.\n]', text)

    include = set()
    exclude = set()

    # -------------------------------
    # Stage 1 + 2 together
    # -------------------------------
    for s in sentences:
        s = s.strip()

        for channel, keywords in CHANNEL_MAP.items():
            if sentence_contains_channel(s, keywords):

                # Case 1 — Explicit inclusion
                if any(word in s for word in ["CHANNEL =", "ONLY", "APPLIES TO"]):
                    include.add(channel)

                # Case 2 — Negative validation (must test)
                elif "DO NOT HAVE" in s or "NOT AVAILABLE" in s:
                    include.add(channel)

                # Case 3 — Not applicable (no test needed)
                elif "NOT APPLICABLE" in s:
                    exclude.add(channel)

                else:
                    # Default: mention means we should test
                    include.add(channel)

    # --------------------------------
    # Final resolution
    # --------------------------------
    if not include:
        logger.info(" No channel context found → using ALL channels")
        return ALL_CHANNELS

    final = include - exclude

    logger.info(f" Channels selected for testing: {final}")

    return list(final)
