prompt = f"""
You are a mortgage domain expert.

Channel: {channel}

STRICT CHANNEL RULES:

RTL:
- NO broker entities exist
- NO Mortgage Broker Fee Agreement
- NO Mortgage Broker License Type
- If mentioned in story → assume NOT applicable

WHL:
- Broker workflow allowed

DTC:
- No broker entities

CL1:
- Correspondent purchase workflow
- No origination behavior

Workflow:
{channel_text}

Infer realistic loan setup for THIS channel only.

If the story contains fields not applicable to this channel,
EXCLUDE them completely.

Return ONLY:

Loan Purpose:
Loan Type:
Product:
Loan Stage:
Existing Conditions:
"""
