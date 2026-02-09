# precondition_extractor.py
import re


class PreconditionExtractor:
    """
    Extract Loan Purpose, Loan Type, Product Code and Loan Stage
    from Acceptance Criteria text.
    """

    PRODUCT_MAP = {
        "CONVENTIONAL": "CF30",
        "FHA": "FF30",
        "VA": "VF30",
        "USDA": "",
        "HELOC": "NRZHeloc",
        "NON QM": "NRSEF30",
    }

    @staticmethod
    def extract(ac_text: str):
        text = ac_text.lower()

        # ---------------- Loan Purpose ----------------
        if "refinance" in text:
            loan_purpose = "Refinance"
        elif "purchase" in text:
            loan_purpose = "Purchase"
        else:
            loan_purpose = ""

        # ---------------- Loan Type ----------------
        loan_type = ""
        for lt in ["fha", "va", "usda", "heloc", "conventional", "non qm"]:
            if lt in text:
                loan_type = lt.upper()
                break

        # ---------------- Product Code ----------------
        product_code = PreconditionExtractor.PRODUCT_MAP.get(loan_type, "")

        # ---------------- Loan Stage ----------------
        if re.search(r"\bapproval\b", text):
            loan_stage = "Approval"
        elif re.search(r"\bsubmission\b", text):
            loan_stage = "Submission"
        elif re.search(r"\bdisclosure\b", text):
            loan_stage = "Disclosure"
        else:
            loan_stage = ""

        return {
            "loan_purpose": loan_purpose,
            "loan_type": loan_type,
            "product_code": product_code,
            "loan_stage": loan_stage
        }
