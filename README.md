import base64
import os
from openai import AzureOpenAI
from dotenv import load_dotenv

load_dotenv()

client = AzureOpenAI(
    api_key=os.getenv("AZURE_OPENAI_KEY"),
    azure_endpoint=os.getenv("AZURE_OPENAI_ENDPOINT"),
    api_version=os.getenv("AZURE_OPENAI_API_VERSION")
)

MODEL = os.getenv("AZURE_OPENAI_VISION_MODEL")  # gpt-4o or gpt-4.1


def _encode_image(image_path: str):
    with open(image_path, "rb") as f:
        return base64.b64encode(f.read()).decode("utf-8")


def extract_ui_knowledge_from_image(image_path: str) -> str:
    """
    Convert UI screenshot into structured QA knowledge
    """

    try:
        img_base64 = _encode_image(image_path)

        prompt = """
You are a Senior Mortgage QA Analyst.

Analyze this UI screenshot and extract TESTABLE SYSTEM BEHAVIOR.

Return structured knowledge in this format:

FIELDS:
- Field Name:
  Type:
  Values:
  Visibility Condition:
  System Impact:

RULES:
- Business rule inferred from UI

Do NOT describe layout.
Do NOT summarize.
Return only QA validation knowledge.
"""

        response = client.chat.completions.create(
            model=MODEL,
            temperature=0,
            messages=[
                {"role": "user", "content": [
                    {"type": "text", "text": prompt},
                    {"type": "image_url",
                     "image_url": {"url": f"data:image/png;base64,{img_base64}"}}
                ]}
            ]
        )

        return "\n--- UI KNOWLEDGE FROM IMAGE ---\n" + response.choices[0].message.content

    except Exception as e:
        return f"[VISION FAILED: {str(e)}]"
