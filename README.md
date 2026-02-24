import logging
import os
from typing import Dict

from langchain_openai import AzureChatOpenAI
from langchain_core.prompts import PromptTemplate
from config.config import get

logger = logging.getLogger(__name__)


class LLMTestcaseGeneratorAgent:

    def __init__(self):

        # Load prompt from file
        prompt_path = get("PROMPT_TEMPLATE_PATH")

        with open(prompt_path, "r", encoding="utf-8") as f:
            prompt_text = f.read()

        # STRICT PIPE FORMAT
        self.prompt = PromptTemplate(
            input_variables=[
                "user_story_id",
                "user_story",
                "description",
                "ac",
                "channel"
            ],
            template=prompt_text,
        )

        # Deterministic output
        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0,  # IMPORTANT: must be 0
        )

        self.chain = self.prompt | self.llm

        os.makedirs("debug", exist_ok=True)

    # ---------------------------------------------------------
    # Generate testcase per channel
    # ---------------------------------------------------------
    def _generate_for_channel(self, state: Dict, channel: str) -> str:

        logger.info(f"🤖 Generating testcase for channel: {channel}")

        payload = {
            "user_story_id": state["user_story_id"],
            "user_story": state["user_story"],
            "description": state["description"],
            "ac": state["acceptance_criteria"],
            "channel": channel
        }

        # Save formatted prompt for debugging
        formatted_prompt = self.prompt.format(**payload)
        debug_file = f"debug/llm_prompt_{state['user_story_id']}_{channel}.txt"

        with open(debug_file, "w", encoding="utf-8") as f:
            f.write(formatted_prompt)

        logger.info(f"📄 Prompt written to: {debug_file}")

        result = self.chain.invoke(payload)

        return result.content.strip()

    # ---------------------------------------------------------
    # LangGraph Entry
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 LLM Generator Running")

        llm_outputs = {}

        for channel in state.get("channels", []):

            output = self._generate_for_channel(state, channel)

            llm_outputs[channel] = output

            print("\n===== GENERATED OUTPUT =====\n")
            print(output)

        state["llm_outputs"] = llm_outputs

        logger.info("✅ LLM Generation Completed")

        return state
