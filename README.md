# product_mapper.py
import random

LOAN_TYPE_PRODUCT_MAP = {
    "CONVENTIONAL": ["CF30", "CF15", "CF10"],
    "CONVENTIONAL JUMBO": ["JCPF30", "JEG10A", "JF30B"],
    "FHA": ["FF30"],
    "VA": ["VF30", "VF15"],
    "USDA": ["UF30"],
    "HELOC": ["NRZHeloc"],
    "NON QM": ["NRSEF30", "NRSVF30"],
}


def resolve_product_code(loan_type: str) -> str:
    if not loan_type:
        return "CF30"

    lt = loan_type.upper().strip()

    for key in LOAN_TYPE_PRODUCT_MAP:
        if key in lt:
            return LOAN_TYPE_PRODUCT_MAP[key][0]  # deterministic default

    return "CF30"
