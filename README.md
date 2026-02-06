Yes. Add a simple MCP tool named get_user_story that:
- Takes user_story_id
- Fetches title, description, acceptance criteria (HTML) from ADO
- Calls process_html_and_images to clean AC and extract OCR text
- Returns structured JSON:
  {
    "user_story_id": "...",
    "title": "...",
    "description": "...",
    "acceptance_criteria": "clean text"
  }
