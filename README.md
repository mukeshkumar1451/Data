import logging
from typing import Dict
from langchain_openai import AzureChatOpenAI
from config.config import get

logger = logging.getLogger(__name__)


class LLMTestcaseGeneratorAgent:

    def __init__(self):

        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0
        )

        self.prompt_file_path = get("PROMPT_FILE_PATH")

    # ---------------------------------------------------------
    # Load Prompt Template
    # ---------------------------------------------------------
    def _load_prompt(self) -> str:
        with open(self.prompt_file_path, "r", encoding="utf-8") as f:
            return f.read()

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("LLM Generator Started (Prompt File Mode)")

        prompt_template = self._load_prompt()
        outputs = {}

        for channel, ctx in state["channel_context"].items():

            prompt = prompt_template.format(
                user_story_id=state["user_story_id"],
                channel=channel,
                user_story=state["user_story"],
                description=state["description"],
                ac=state["acceptance_criteria"],
                precondition=ctx.get("precondition", ""),
                historical_scenario=ctx.get("historical_scenario", ""),
                historical_script=ctx.get("historical_script", ""),
                historical_steps=ctx.get("historical_steps", "")
            )

            response = self.llm.invoke(prompt)

            outputs[channel] = response.content.strip()

        state["llm_outputs"] = outputs

        logger.info("LLM Generator Completed")

        return state
