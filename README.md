import logging
from typing import Dict

from langchain_openai import AzureChatOpenAI
from langchain_core.prompts import PromptTemplate

from config.config import get
from state.rag_state import RAGState

logger = logging.getLogger(__name__)


class LLMTestcaseGeneratorAgent:
    """
    Channel-safe LangChain-based LLM agent.

    Responsibilities:
      • Load production-grade channel-safe prompt
      • Inject channel-specific variables
      • Inject retrieved knowledge
      • Enforce channel isolation
      • Generate raw testcase text per channel
    """

    # =========================================================
    # INIT
    # =========================================================
    def __init__(self):

        # ---------------- Load prompt from file ----------------
        prompt_path = get("PROMPT_TEMPLATE_PATH")

        with open(prompt_path, "r", encoding="utf-8") as f:
            prompt_text = f.read()

        # 🔥 UPDATED INPUT VARIABLES (MUST MATCH PROMPT FILE)
        self.prompt = PromptTemplate(
            input_variables=[
                "channel",
                "channel_rules",
                "channel_specific_context",
                "historical_context",
                "precondition",
                "retrieved_docs",
                "user_story",
                "user_story_id"
            ],
            template=prompt_text,
        )

        # ---------------- Azure GPT via LangChain ----------------
        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0.2,
        )

        self.chain = self.prompt | self.llm

    # =========================================================
    # Convert retrieved docs into historical context
    # =========================================================
    def _build_context(self, docs: Dict) -> str:

        context = ""

        # Flow
        if docs.get("flow"):
            context += "\n===== SYSTEM FLOW =====\n"
            for d in docs["flow"]:
                context += d.get("content", "") + "\n"

        # Rules
        if docs.get("rules"):
            context += "\n===== BUSINESS RULES =====\n"
            for d in docs["rules"]:
                context += d.get("content", "") + "\n"

        # Guidelines
        if docs.get("guidelines"):
            context += "\n===== TEST WRITING GUIDELINES =====\n"
            for d in docs["guidelines"]:
                context += d.get("content", "") + "\n"

        # Historical Tests
        if docs.get("tests"):
            context += "\n===== HISTORICAL TESTCASES =====\n"
            for d in docs["tests"]:
                context += f"\nTestCase: {d.get('testCaseId')}\n"
                context += d.get("content", "") + "\n"

        return context.strip()

    # =========================================================
    # Generate testcase for one channel
    # =========================================================
    def _generate_for_channel(
        self, state: RAGState, channel: str, docs: Dict
    ) -> str:

        logger.info(f"🤖 Generating testcase for channel: {channel}")

        # ---------------- Core Inputs ----------------
        user_story = state.get("user_story", "")
        user_story_id = state.get("user_story_id", "")

        # Channel-specific workflow
        channel_context_map = state.get("channel_context_map", {})
        channel_specific_context = channel_context_map.get(channel, "")

        if not channel_specific_context:
            logger.warning(f"⚠️ No channel-specific context found for {channel}")

        # Channel rules
        channel_rules_map = state.get("channel_rules", {})
        channel_rules = channel_rules_map.get(channel, "")

        # Retrieved knowledge
        historical_context = self._build_context(docs)

        # Realistic setup
        precondition = state.get("channel_setup", {}).get(channel, "")
        logger.info(f"📌 Precondition for {channel}:\n{precondition}\n")

        # Extract simplified retrieved text (for explicit RAG injection)
        retrieved_text = ""
        for d in docs.get("tests", []):
            retrieved_text += "\n" + d.get("content", "")

        # ---------------- Invoke LLM ----------------
        result = self.chain.invoke(
            {
                "channel": channel,
                "channel_rules": channel_rules,
                "channel_specific_context": channel_specific_context,
                "historical_context": historical_context,
                "precondition": precondition,
                "retrieved_docs": retrieved_text.strip(),
                "user_story": user_story,
                "user_story_id": user_story_id
            }
        )

        logger.info(f"✅ LLM output received for {channel}")

        return result.content

    # =========================================================
    # LangGraph Node Entry
    # =========================================================
    def run(self, state: RAGState) -> RAGState:

        logger.info("🚀 LLM Testcase Generator Agent started")

        llm_outputs = {}

        for channel, docs in state.get("retrieved_docs", {}).items():

            if not docs:
                logger.warning(f"⚠️ No retrieved docs for channel {channel}")
                continue

            llm_text = self._generate_for_channel(state, channel, docs)
            llm_outputs[channel] = llm_text

        state["llm_outputs"] = llm_outputs

        logger.info("🎯 LLM generation completed for all channels")

        return state
