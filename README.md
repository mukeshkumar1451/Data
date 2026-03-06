CHANNEL: {channel}

STRICT CHANNEL ENTITY FILTER

The generated test steps MUST comply with the following rules.

If CHANNEL is RTL or DTC:

The output MUST NOT contain any reference to the following entities:

Mortgage Broker
Broker License
Broker Compensation
Broker Fee Agreement
Manage Broker Disclosures
Mortgage Broker License Type

If any of these entities appear in historical workflow examples,
they MUST be completely ignored and must NOT appear in the generated test steps.

If this rule is violated, the generated output is invalid.

------------------------------------------------------------

If CHANNEL is WHL or CL1:

Mortgage Broker entities may appear ONLY when explicitly required
by the acceptance criteria.

Do not introduce broker-related steps unless acceptance criteria require them.
====================================
Historical Workflow Usage Rule

Historical workflow is provided only to understand navigation patterns.

Historical steps may contain channel-specific behavior.

When historical workflow conflicts with channel rules,
CHANNEL RULES ALWAYS TAKE PRIORITY.

Never copy or reproduce channel-incompatible steps.
