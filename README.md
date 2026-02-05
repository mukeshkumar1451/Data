from openai import AzureOpenAI
from config import get


class LLMTestCaseGenerator:

    def __init__(self):
        self.client = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT")
        )
        self.model = get("CHAT_MODEL")
        self.prompt_path = get("PROMPT_PATH")

    def _load_prompt(self):
        with open(self.prompt_path, "r") as f:
            return f.read()

    def generate(self, user_story, description, ac, historical_context):
        base_prompt = self._load_prompt()

        final_prompt = f"""
{base_prompt}

----------------------------
User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}

----------------------------
Historical Test Cases:
{historical_context}
"""

        response = self.client.chat.completions.create(
            model=self.model,
            messages=[{"role": "user", "content": final_prompt}],
            temperature=0.2
        )

        return response.choices[0].message.content
