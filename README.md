def build_channel_rules(channel: str) -> str:

    rules = {
        "RTL": """
Retail channel rules:

- Loan officer + borrower interaction only.
- NO Mortgage broker entities exist in this channel.
- Mortgage Broker Fee Agreement does NOT exist.
- Mortgage Broker License Type does NOT exist.
- Broker compensation fields must be COMPLETELY ignored.
- Do NOT validate visibility.
- Do NOT validate privilege.
- Do NOT mention broker fields in any step.
- Validate consumer disclosure behavior only.
""",

        "WHL": """
Wholesale channel rules:

- Broker originated loan.
- Mortgage Broker Fee Agreement and License Type apply.
- Broker compensation and license validations required.
- Submission package and broker documentation expected.
""",

        "DTC": """
DTC channel rules:

- Pure self-service borrower workflow.
- No loan officer and no Mortgage broker interaction.
- Broker compensation fields do NOT exist.
- Do NOT validate broker fields.
- Automated disclosure generation expected.
""",

        "CL1": """
Correspondent channel rules:

- Loan already originated externally.
- Purchase advice and acquisition validations apply.
- No borrower application workflow.
- No broker origination workflow.
"""
    }

    return rules.get(channel, "")
