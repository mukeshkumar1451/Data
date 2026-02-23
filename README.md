import logging
from typing import Dict

from langchain_openai import AzureChatOpenAI
from langchain_core.prompts import PromptTemplate

from config.config import get
from state.rag_state import RAGState

logger = logging.getLogger(__name__)


class LLMTestcaseGeneratorAgent:
    """
    Stable Production Version
    - No channel_specific_context
    - Full AC used
    - Channel filtering handled via:
        • Azure Search filtering
        • channel_rules
    """

    def __init__(self):

        prompt_path = get("PROMPT_TEMPLATE_PATH")

        with open(prompt_path, "r", encoding="utf-8") as f:
            prompt_text = f.read()

        # 🔥 REMOVE channel_specific_context
        self.prompt = PromptTemplate(
            input_variables=[
                "user_story_id",
                "user_story",
                "description",
                "ac",
                "historical_context",
                "precondition",
                "channel_rules",
                "retrieved_docs",
                "channel"
            ],
            template=prompt_text,
        )

        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0.2,
        )

        self.chain = self.prompt | self.llm

    # ---------------------------------------------------------
    # Convert retrieved docs into RAG text
    # ---------------------------------------------------------
    def _build_retrieved_text(self, docs: Dict) -> str:

        text = ""

        # Only inject top few tests (avoid overpowering story)
        for d in docs.get("tests", [])[:5]:
            content = d.get("content", "")
            text += "\n--- Historical Test ---\n"
            text += content[:1200]  # limit size

        return text[:6000]  # token safety

    # ---------------------------------------------------------
    # Generate testcase per channel
    # ---------------------------------------------------------
    def _generate_for_channel(self, state: RAGState, channel: str, docs: Dict) -> str:

        logger.info(f"🤖 Generating testcase for channel: {channel}")

        retrieved_text = self._build_retrieved_text(docs)

        precondition = state.get("channel_setup", {}).get(channel, "")
        channel_rules = state.get("channel_rules", {}).get(channel, "")

        result = self.chain.invoke(
            {
                "user_story_id": state["user_story_id"],
                "user_story": state["user_story"],
                "description": state["description"],
                "ac": state["acceptance_criteria"],
                "historical_context": "",   # optional
                "retrieved_docs": retrieved_text,
                "precondition": precondition,
                "channel_rules": channel_rules,
                "channel": channel
            }
        )

        return result.content

    # ---------------------------------------------------------
    # LangGraph Entry
    # ---------------------------------------------------------
    def run(self, state: RAGState) -> RAGState:

        logger.info("🚀 LLM Testcase Generator Agent started")

        llm_outputs = {}

        for channel, docs in state["retrieved_docs"].items():

            if not docs:
                logger.warning(f"No docs for {channel}")
                continue

            llm_text = self._generate_for_channel(state, channel, docs)
            llm_outputs[channel] = llm_text

        state["llm_outputs"] = llm_outputs

        logger.info("✅ LLM generation completed")
        return state
