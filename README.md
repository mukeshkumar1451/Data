# -*- coding: utf-8 -*-
# utils/channel_detector.py

import re
import logging

logger = logging.getLogger(__name__)

# ---------------------------------------------------
# Known channels
# ---------------------------------------------------
ALL_CHANNELS = ["RTL", "WHL", "DTC", "CL1"]

CHANNEL_MAP = {
    "RTL": ["RTL", "RET", "RETAIL"],
    "WHL": ["WHL", "WHOLESALE"],
    "DTC": ["DTC", "DIRECT TO CUSTOMER"],
    "CL1": ["CL1", "CORRESPONDENT"]
}


# ---------------------------------------------------
# Helper
# ---------------------------------------------------
def sentence_contains_channel(sentence, keywords):
    return any(k in sentence for k in keywords)


# ---------------------------------------------------
# NEW: Behavioral Channel Detection (Mortgage Aware)
# ---------------------------------------------------
def detect_by_behavior(text: str) -> list:
    """
    Detect channel using workflow meaning instead of keywords.
    Runs only when explicit channel not found.
    """

    t = text.lower()
    detected = set()

    # ---------- WHOLESALE ----------
    whl_signals = [
        "broker",
        "originator",
        "create loan on behalf",
        "h2o",
        "broker id",
        "company picker",
        "business unit",
        "broker employees"
    ]

    if sum(1 for s in whl_signals if s in t) >= 3:
        detected.add("WHL")

    # ---------- RETAIL ----------
    rtl_signals = [
        "loan officer",
        "borrower",
        "customer portal",
        "retail user",
        "face to face"
    ]

    if sum(1 for s in rtl_signals if s in t) >= 3:
        detected.add("RTL")

    # ---------- DTC ----------
    dtc_signals = [
        "consumer",
        "self service",
        "online application",
        "borrower completes application",
        "no loan officer"
    ]

    if sum(1 for s in dtc_signals if s in t) >= 2:
        detected.add("DTC")

    # ---------- CORRESPONDENT ----------
    cl1_signals = [
        "correspondent",
        "delegated",
        "non delegated",
        "purchase advice",
        "loan purchase"
    ]

    if sum(1 for s in cl1_signals if s in t) >= 2:
        detected.add("CL1")

    return list(detected)


# ---------------------------------------------------
# MAIN DETECTOR
# ---------------------------------------------------
def detect_channels(ac_text: str) -> list:

    logger.info("Intelligent channel detection started...")

    text = ac_text.upper()
    sentences = re.split(r'[.\n]', text)

    include = set()
    exclude = set()

    # -------------------------------
    # Stage 1 & 2 — Explicit Detection
    # -------------------------------
    for s in sentences:
        s = s.strip()

        for channel, keywords in CHANNEL_MAP.items():
            if sentence_contains_channel(s, keywords):

                # Explicit inclusion
                if any(word in s for word in ["CHANNEL =", "ONLY", "APPLIES TO"]):
                    include.add(channel)

                # Negative validation (must test)
                elif "DO NOT HAVE" in s or "NOT AVAILABLE" in s:
                    include.add(channel)

                # Not applicable
                elif "NOT APPLICABLE" in s:
                    exclude.add(channel)

                # Default mention
                else:
                    include.add(channel)

    # --------------------------------
    # Stage 3 — Behavioral Detection
    # --------------------------------
    if not include:
        logger.info(" No explicit channel → trying behavioral detection")

        behavioral = detect_by_behavior(ac_text)

        if behavioral:
            logger.info(f" Behavior detected channels: {behavioral}")
            return behavioral

        logger.info(" No behavior detected → using ALL channels")
        return ALL_CHANNELS

    # --------------------------------
    # Final resolution
    # --------------------------------
    final = include - exclude

    logger.info(f" Channels selected for testing: {final}")

    return list(final)
