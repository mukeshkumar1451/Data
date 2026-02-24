class ADOIntelligenceAgent:

    def __init__(self):
        from config.config import get
        from openai import AzureOpenAI

        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )

        self.model = get("CHAT_MODEL")
