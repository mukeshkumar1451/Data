import re


class PreconditionExtractor:

    PRODUCT_MAP = {
        "CONVENTIONAL": "CF30",
        "FHA": "FF30",
        "VA": "VF30",
        "USDA": "",
        "HELOC": "NRZHeloc",
        "NON QM": "NRSEF30",
    }

    @staticmethod
    def extract(precondition_text: str):
        text = precondition_text.lower()

        # Loan Purpose
        if "refinance" in text:
            loan_purpose = "Refinance"
        elif "purchase" in text:
            loan_purpose = "Purchase"
        else:
            loan_purpose = ""

        # Loan Type
        loan_type = ""
        for lt in ["fha", "va", "usda", "heloc", "conventional", "non qm"]:
            if lt in text:
                loan_type = lt.upper()
                break

        # Product Code
        product_code = PreconditionExtractor.PRODUCT_MAP.get(loan_type, "")

        # Loan Stage
        if "approval" in text:
            loan_stage = "Approval"
        elif "submission" in text:
            loan_stage = "Submission"
        elif "disclosure" in text:
            loan_stage = "Disclosure"
        else:
            loan_stage = ""

        return loan_purpose, loan_type, product_code, loan_stage
