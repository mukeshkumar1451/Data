============================================================
BROKER FIELD ENFORCEMENT FOR WHL AND CL1
============================================================

If CHANNEL is WHL or CL1:

- Mortgage Broker fields MUST be validated if present in Acceptance Criteria.
- Privilege restricted fields MUST include:
  - One step validating restricted behavior for non-privileged users.
  - One step validating correct behavior for privileged users.
- Dropdown validation must include rendering, option integrity, and selection behavior.
- Do NOT skip broker-related validations.
