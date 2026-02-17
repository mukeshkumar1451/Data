# utils/product_mapper.py

# ---------------------------------------------------------
# Product Rules (Domain Accurate)
# ---------------------------------------------------------

PRIMARY_PRODUCTS = {
    "CONVENTIONAL": ["CF30", "CF15", "CF10"],
    "CONVENTIONAL JUMBO": ["JCPF30", "JEG10A", "JF30B"],
    "FHA": ["FF30"],
    "VA": ["VF30", "VF15"],
    "USDA": ["CF30"],
    "HELOC": ["NRZHeloc"],
    "NON QM": ["NRSEF30", "NRSVF30"],
}

SECOND_LIEN_PRODUCTS = ["SASF30A", "SASF30B", "SASF30C"]

HELOC_ALLOWED_CHANNELS = ["RTL", "DTC"]


# ---------------------------------------------------------
# Resolver
# ---------------------------------------------------------

def resolve_product_code(
    loan_type: str,
    channel: str = None,
    lien_type: str = "First Lien",
    doc_type: str = "Full"
) -> str:
    """
    Smart deterministic product resolver.
    Prevents impossible combinations.
    """

    if not loan_type:
        return "CF30"

    lt = loan_type.upper().strip()
    lien = (lien_type or "").upper()

    # -----------------------------
    # Second lien overrides all
    # -----------------------------
    if "SECOND" in lien:
        return SECOND_LIEN_PRODUCTS[0]

    # -----------------------------
    # HELOC restrictions
    # -----------------------------
    if "HELOC" in lt:
        if channel in HELOC_ALLOWED_CHANNELS:
            return "NRZHeloc"
        else:
            return "CF30"  # fallback safe loan

    # -----------------------------
    # Standard mapping
    # -----------------------------
    for key in PRIMARY_PRODUCTS:
        if key in lt:
            return PRIMARY_PRODUCTS[key][0]

    # -----------------------------
    # Safe default
    # -----------------------------
    return "CF30"
