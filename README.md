This tool performs end-to-end automated test case generation from an Azure DevOps User Story.

Given a user_story_id, the tool will:

1. Fetch the user story from Azure DevOps (title, description, acceptance criteria with HTML and images).
2. Extract images from the acceptance criteria and perform OCR to capture UI flow text.
3. Clean and merge OCR text with acceptance criteria.
4. Detect applicable channels (RTL, WHL, DTC, CL1) from the acceptance criteria.
5. Perform semantic vector search in Azure AI Search for historical test cases.
6. Apply hybrid retrieval with LLM re-ranking for best matching test cases.
7. Generate new channel-specific test cases using Azure OpenAI based on historical patterns.
8. Export the generated test cases into a multi-sheet Excel file using the project template.

This tool requires only:
- user_story_id

Output:
An Excel test script file saved to the project output folder.
