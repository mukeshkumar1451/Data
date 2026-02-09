def extract_preconditions(ac_text: str):
    text = ac_text.lower()

    # Loan Purpose
    if "refinance" in text:
        loan_purpose = "Refinance"
    elif "purchase" in text:
        loan_purpose = "Purchase"
    else:
        loan_purpose = ""

    # Loan Type
    for t in ["fha", "va", "usda", "heloc", "conventional"]:
        if t in text:
            loan_type = t.upper()
            break
    else:
        loan_type = ""

    # Product mapping
    product_map = {
        "FHA": "FF30",
        "VA": "VF30",
        "USDA": "",
        "HELOC": "NRZHeloc",
        "CONVENTIONAL": "CF30",
    }
    product_code = product_map.get(loan_type, "")

    # Loan Stage
    if "approval" in text:
        loan_stage = "Approval"
    elif "submission" in text:
        loan_stage = "Submission"
    else:
        loan_stage = ""

    return loan_purpose, loan_type, product_code, loan_stage
