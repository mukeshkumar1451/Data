# utils/loan_setup_normalizer.py

import re
from typing import Dict

# ---------------------------------------------------------
# Allowed Domain Values
# ---------------------------------------------------------

LOAN_TYPES = ["Conventional", "FHA", "VA", "USDA", "Heloc"]

PRODUCT_MAP = {
    "Conventional": "CF30",
    "FHA": "FF30",
    "VA": "VF30",
    "USDA": "CF30",
    "Heloc": "NRZHeloc"
}

LIEN_TYPES = ["First Lien", "Second Lien"]
DOC_TYPES = ["Full", "Streamline"]

# Channel stage restrictions
CHANNEL_STAGES = {
    "RTL": [
        "Application Accepted","In-Processing","UW Submitted","Approved W/Conditions",
        "Conditions Submitted","Conditions In Review","Final Approval In Review",
        "Clear To Close","Closing Disclosure Sent","Funds Released"
    ],
    "WHL": [
        "Created","Submission Review","UW Submitted","Approved W/Conditions",
        "Clear To Close","Closing Disclosure Sent","Funds Released"
    ],
    "DTC": [
        "Application Accepted","CD Audit Submitted","CD Audit Completed",
        "UW Submitted","Approved W/Conditions","Clear To Close","Funds Released"
    ],
    "CL1": [
        "Created","UW Submitted","Approved W/Conditions",
        "Clear To Close","Funds Released"
    ]
}

# ---------------------------------------------------------
# Helpers
# ---------------------------------------------------------

def _extract(text: str, key: str) -> str:
    match = re.search(rf"{key}:\s*(.+)", text, re.IGNORECASE)
    return match.group(1).strip() if match else ""


def _closest(value: str, allowed: list, default: str):
    for a in allowed:
        if a.lower() in value.lower():
            return a
    return default

# ---------------------------------------------------------
# Main Normalizer
# ---------------------------------------------------------

def normalize_setup(channel: str, setup_text: str) -> Dict:

    purpose = _extract(setup_text, "Loan Purpose")
    ltype = _extract(setup_text, "Loan Type")
    stage = _extract(setup_text, "Loan Stage")

    # ---- normalize loan type
    loan_type = _closest(ltype, LOAN_TYPES, "Conventional")

    # ---- lien logic
    lien = "Second Lien" if "second" in setup_text.lower() else "First Lien"

    # ---- doc type logic
    doc = "Streamline" if "streamline" in setup_text.lower() else "Full"

    # ---- stage normalization by channel
    allowed_stages = CHANNEL_STAGES[channel]
    loan_stage = _closest(stage, allowed_stages, allowed_stages[0])

    # ---- product mapping
    product_code = PRODUCT_MAP.get(loan_type, "CF30")

    return {
        "purpose": purpose or "Purchase",
        "loan_type": loan_type,
        "product_code": product_code,
        "lien": lien,
        "doc_type": doc,
        "stage": loan_stage
    }
