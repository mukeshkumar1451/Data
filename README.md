import re

# =========================================================
# 1. PRODUCT MAPPING (Domain Truth)
# =========================================================

LOAN_TYPE_PRODUCT_MAP = {
    "CONVENTIONAL": "CF30",
    "CONVENTIONAL JUMBO": "JCPF30",
    "FHA": "FF30",
    "VA": "VF30",
    "USDA": "UF30",
    "HELOC": "NRZHeloc",
    "NON QM": "NRSEF30",
    "SECOND LIEN": "SASF30A"
}


# ---------------------------------------------------------
# Robust extractor (handles LLM formatting noise)
# ---------------------------------------------------------
def _extract(label: str, text: str) -> str:
    if not text:
        return ""

    patterns = [
        rf"{label}\s*:\s*(.*)",
        rf"{label}\s*-\s*(.*)",
        rf"\*\*{label}\*\*\s*:\s*(.*)",
    ]

    for p in patterns:
        m = re.search(p, text, re.IGNORECASE)
        if m:
            return m.group(1).strip()

    return ""


# =========================================================
# 2. LOAN TYPE NORMALIZATION
# =========================================================

def normalize_loan_type(raw: str) -> str:
    if not raw:
        return "Conventional"

    raw_upper = raw.upper()

    for key in LOAN_TYPE_PRODUCT_MAP:
        if key in raw_upper:
            return key.title()

    return "Conventional"


def resolve_product_code(loan_type: str) -> str:
    key = loan_type.upper()
    return LOAN_TYPE_PRODUCT_MAP.get(key, "CF30")


# =========================================================
# 3. STAGE NORMALIZATION
# =========================================================

CHANNEL_STAGES = {

    "RTL": [
        "Application Accepted","In-Processing","UW Submitted",
        "Approved W/Conditions","Conditions Submitted",
        "Conditions In Review","Final Approval In Review",
        "Clear To Close","Closing Disclosure Ordered",
        "Closing Disclosure Sent","Closing Docs sent",
        "Funds Ordered","Funds Sent","Funds Released"
    ],

    "WHL": [
        "Created","LE Sent","Submission Review","UW Submitted",
        "Approved W/Conditions","Conditions Submitted",
        "Conditions In Review","Final Approval In Review",
        "Clear To Close","Closing Disclosure Ordered",
        "Closing Disclosure Sent","Closing Docs sent",
        "Funds Ordered","Funds Sent","Funds Released"
    ],

    "DTC": [
        "Application Accepted","CD Audit Submitted","CD Audit Completed",
        "UW Submitted","Approved W/Conditions","Conditions Submitted",
        "Conditions In Review","Final Approval In Review",
        "Clear To Close","Closing Disclosure Ordered",
        "Closing Disclosure Sent","Closing Docs sent",
        "Funds Ordered","Funds Sent","Funds Released"
    ],

    "CL1": [
        "Created","UW Submitted","Approved W/Conditions",
        "Conditions Submitted","Conditions In Review",
        "Final Approval In Review","Clear To Close",
        "Closing Disclosure Ordered","Closing Disclosure Sent",
        "Closing Docs sent","Correspondent Funded",
        "Loan Purchase Review","Purchase Wire Review",
        "Approved For Purchase","Funds Released"
    ]
}


STAGE_SYNONYMS = {
    "approved with conditions": "Approved W/Conditions",
    "approval with conditions": "Approved W/Conditions",
    "generate disclosures": "Closing Disclosure Ordered",
    "disclosure generation": "Closing Disclosure Ordered",
    "cd generated": "Closing Disclosure Sent",
    "ctc": "Clear To Close",
}


def normalize_stage(channel: str, raw_stage: str) -> str:

    if not raw_stage:
        return CHANNEL_STAGES[channel][0]

    stage = raw_stage.lower()

    # handle ranges like "Application Accepted through CD Audit Completed"
    if "through" in stage or "to" in stage:
        parts = re.split(r"through|to", stage)
        stage = parts[-1].strip()

    # synonym mapping
    for key, value in STAGE_SYNONYMS.items():
        if key in stage:
            return value

    # fuzzy match allowed stages
    for allowed in CHANNEL_STAGES[channel]:
        if allowed.lower() in stage:
            return allowed

    return CHANNEL_STAGES[channel][0]


# =========================================================
# 4. PURPOSE NORMALIZATION
# =========================================================

def normalize_purpose(raw: str) -> str:

    if not raw:
        return "Purchase"

    raw = raw.lower()

    if "refi" in raw:
        return "Refinance"
    if "construction" in raw:
        return "Construction Permanent"

    return "Purchase"


# =========================================================
# MAIN ENTRY — CONTRACT GUARANTEED STRUCTURE
# =========================================================

def normalize_full_setup(channel: str, setup_text: str) -> dict:

    purpose_raw = _extract("Loan Purpose", setup_text)
    type_raw = _extract("Loan Type", setup_text)
    stage_raw = _extract("Loan Stage", setup_text)

    purpose = normalize_purpose(purpose_raw)
    loan_type = normalize_loan_type(type_raw)
    product = resolve_product_code(loan_type)
    stage = normalize_stage(channel, stage_raw)

    # IMPORTANT: standardized keys used by Excel agent
    return {
        "loan_purpose": purpose,
        "loan_type": loan_type,
        "product_code": product,
        "loan_stage": stage
    }
