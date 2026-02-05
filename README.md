user_story_id: "7123445"

user_story: |
  Part 2: FHA Case Number Request Screen - Detached Condo Defaults to YES

description: |
  As a user,
  I want the “Is the Property a Condo?” question on the FHA Case Number > Type of Case section
  to default to YES when the Type of Property is Detached Condominium,
  so that the system behavior is consistent for all condominium property types
  and reduces manual corrections.

  Observed behavior on Wholesale loans in H2O:

  When Type of Property = Condominium or High-Rise Condominium,
  the “Is the Property a Condo?” question defaults to YES.

  When Type of Property = Detached Condominium,
  the question defaults to NO.

acceptance_criteria: |
  AC1: Default "Is the Property a Condo?" to YES for Detached Condominium

  Given the loan is a Wholesale loan
  And FHA Request Case Number screen is available
  And the Type of Property = "Detached Condominium"
  When the FHA Case Number > Type of Case section is displayed
  Then the "Is the Property a Condo?" question should default to "YES"

  Note to Dev:
  Currently for Condominium and High-Rise Condominium, the field defaults to YES.
  The same behavior must apply for Detached Condominium.
