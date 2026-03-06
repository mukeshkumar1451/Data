Step-by-Step Actions Guide

Follow these principles strictly when generating test case steps.

Atomic Steps
Write one action per step using imperative verbs such as:
Launch
Click
Enter
Select
Navigate
Submit
Upload
Download

Natural Language Navigation
Describe navigation clearly and conversationally.

Example:
Launch the application URL.
You will land on the home dashboard.
Click the Settings option in the left navigation panel.

Each sentence represents a single step.

Field Details
Mention field names and field types whenever interacting with UI elements.

Supported field types:
text box
dropdown
checkbox
radio button
date picker
button

Explicit Page Transitions
Always describe the resulting page after an action.

Example:
After clicking Next, the system navigates to the User Details page.

Functionality Coverage
Test steps must include interactions validating business functionality such as:
Submitting forms
Saving or updating records
Triggering workflows
Searching or filtering records
Performing calculations
Uploading or downloading files

Expected Result Per Step
Each step must include the system behavior immediately after the action.

Example:
After clicking Save, the system stores the record and loads the confirmation page.

Test Data
Use only the test data provided in context files.
Do not invent values.
