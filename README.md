import logging
import os
from typing import Dict

from langchain_openai import AzureChatOpenAI
from langchain_core.prompts import PromptTemplate
from config.config import get

logger = logging.getLogger(__name__)


class LLMTestcaseGeneratorAgent:

    def __init__(self):

        # Load prompt path from .env
        prompt_path = get("PROMPT_TEMPLATE_PATH")

        if not os.path.exists(prompt_path):
            raise FileNotFoundError(f"Prompt file not found: {prompt_path}")

        with open(prompt_path, "r", encoding="utf-8") as f:
            prompt_text = f.read()

        self.prompt = PromptTemplate(
            input_variables=[
                "user_story_id",
                "user_story",
                "description",
                "ac",
                "channel",
                "precondition"
                "historical_steps"
            ],
            template=prompt_text
        )

        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0  # deterministic
        )

        self.chain = self.prompt | self.llm

        logger.info("✅ LLM Testcase Generator initialized")

    # ---------------------------------------------------------
    # LangGraph Entry
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("🤖 LLM Generator Running")

        # 🔥 clone full state safely
        new_state = dict(state)

        outputs = {}

        for channel, ctx in state["channel_context"].items():

            payload = {
                "user_story_id": state["user_story_id"],
                "user_story": state["user_story"],
                "description": state["description"],
                "ac": state["acceptance_criteria"],
                "channel": channel,
                "precondition": ctx["precondition"],
                "historical_steps": ctx["historical_steps"]
            }

            result = self.chain.invoke(payload)
            outputs[channel] = result.content.strip()

        new_state["llm_outputs"] = outputs
        
       # print("LLM Outputs:", outputs)  # Debugging line to check LLM outputs

        # 🔥 DO NOT TOUCH selected_preconditions
        # Just preserve whatever came from retrieval

        return new_state

