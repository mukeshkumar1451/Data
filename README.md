CHANNEL ENTITY ENFORCEMENT (STRICT)

If CHANNEL is RTL or DTC:

The generated output MUST NOT contain any reference to the following entities:

Mortgage Broker
Broker License
Broker Compensation
Broker Fee Agreement
Mortgage Broker License Type
Manage Broker Disclosures

These entities must NOT appear in:

- step descriptions
- screen names
- expected results
- validation statements

Do NOT generate validation steps checking their absence.

If these entities appear in historical workflow examples,
they MUST be ignored completely.

Any output containing these entities is invalid.
