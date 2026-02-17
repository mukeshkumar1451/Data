# utils/channel_detector.py
import re
import logging

logger = logging.getLogger(__name__)

ALL_CHANNELS = ["RTL", "WHL", "DTC", "CL1"]


# -------------------------------------------------
# Behavioral Channel Detection
# -------------------------------------------------
def detect_channels(text: str) -> list:

    logger.info("Behavioral channel detection started...")

    t = text.upper()

    # ---------------------------
    # 1. Persona Detection (strongest signal)
    # ---------------------------
    if "NON-BROKER USER IN H2O" in t or "INTERNAL USER" in t:
        logger.info("Detected INTERNAL H2O user → WHL")
        return ["WHL"]

    if "BROKER PORTAL" in t or "BROKER LO" in t:
        logger.info("Detected Broker persona → WHL")
        return ["WHL"]

    if "CUSTOMER PORTAL" in t or "BORROWER" in t:
        logger.info("Detected Borrower persona → RTL")
        return ["RTL"]

    if "IGNITE" in t or "DIRECT TO CONSUMER" in t:
        logger.info("Detected Ignite flow → DTC")
        return ["DTC"]

    if "CORRESPONDENT" in t or "CL1" in t:
        logger.info("Detected Correspondent → CL1")
        return ["CL1"]

    # ---------------------------
    # 2. Feature based detection
    # ---------------------------
    if "BUSINESS UNIT" in t or "CREATE LOAN ON BEHALF OF" in t:
        logger.info("Detected internal operations feature → WHL")
        return ["WHL"]

    # ---------------------------
    # Fallback
    # ---------------------------
    logger.info("No strong signal → using ALL channels")
    return ALL_CHANNELS
