def build_channel_rules(channel: str) -> str:

    rules = {
        "RTL": """
Retail channel rules:
- Loan officer + borrower interaction only
- NO Mortgage broker entities exist
- NO correspondent purchase flow
- Broker compensation fields must NOT appear
- Validate consumer disclosure behavior only
""",

        "WHL": """
Wholesale channel rules:
- Broker originated loan
- Broker compensation and license validations apply
- Submission package and broker docs expected
""",

        "DTC": """
DTC channel rules:
- Pure self-service borrower workflow
- No loan officer and no Mortgage broker interaction
- Automated disclosure generation expected
""",

        "CL1": """
Correspondent channel rules:
- Loan already originated externally
- Purchase advice and acquisition validations apply
- No borrower application workflow
"""
    }

    return rules.get(channel, "")
