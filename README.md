import base64
from langchain_openai import AzureChatOpenAI
from config.config import get


llm_vision = AzureChatOpenAI(
    api_key=get("AZURE_OPENAI_KEY"),
    azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
    api_version="2024-12-01-preview",
    deployment_name=get("AZURE_OPENAI_VISION_DEPLOYMENT"),  # VERY IMPORTANT
    temperature=0
)


def _encode_image(path: str) -> str:
    with open(path, "rb") as f:
        return base64.b64encode(f.read()).decode()


def extract_ui_knowledge_from_image(image_path: str) -> str:
    """
    Understand mortgage UI screenshot and extract business logic
    """

    img_b64 = _encode_image(image_path)

    response = llm_vision.invoke([
        {
            "role": "system",
            "content": "You are a mortgage QA analyst extracting validation rules from UI screenshots."
        },
        {
            "role": "user",
            "content": [
                {"type": "text", "text": """
Analyze this mortgage application screenshot and extract:

1. Fields and dropdowns
2. Mandatory conditions
3. Visibility rules
4. Validation rules
5. Dependencies between fields

Return only structured bullet points.
"""},

                {
                    "type": "image_url",
                    "image_url": {"url": f"data:image/png;base64,{img_b64}"}
                }
            ]
        }
    ])

    return response.content
