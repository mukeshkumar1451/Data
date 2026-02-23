import logging
from typing import Dict, List

from langchain_openai import AzureChatOpenAI
from langchain_core.prompts import PromptTemplate

from config.config import get
from state.rag_state import RAGState

logger = logging.getLogger(__name__)


class LLMTestcaseGeneratorAgent:
    """
    LangChain-based LLM agent.

    Responsibilities:
      • Load prompt from .txt file
      • Inject dynamic variables
      • Call Azure GPT
      • Generate raw testcase text per channel
    """

    def __init__(self):

        # ---------------- Load prompt from file ----------------
        prompt_path = get("PROMPT_TEMPLATE_PATH")

        with open(prompt_path, "r", encoding="utf-8") as f:
            prompt_text = f.read()

        self.prompt = PromptTemplate(
            input_variables=[
                "user_story_id",
                "user_story",
                "description",
                "ac",
                "historical_context",
                "qa_style_rules",
                "precondition"
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

    # ---------------------------------------------------------
    # Convert retrieved docs into historical context
    # ---------------------------------------------------------
    def _build_context(self, docs: Dict) -> str:

       context = "\n===== SYSTEM FLOW =====\n"
       for d in docs["flow"]:
        context += d["content"] + "\n"

       context += "\n===== BUSINESS RULES =====\n"
       for d in docs["rules"]:
        context += d["content"] + "\n"
        
       context += "\n===== TEST WRITING GUIDELINES =====\n"
       for d in docs["guidelines"]:
        context += d["content"] + "\n"
        
       for d in docs["tests"]:
        context += f"\nTestCase: {d.get('testCaseId')}\n{d['content']}\n"

       return context


    # ---------------------------------------------------------
    # Generate testcase for one channel
    # ---------------------------------------------------------
    def _generate_for_channel(
        self, state: RAGState, channel: str, docs: List[Dict]
    ) -> str:

        user_story = state["user_story"]
        description = state["description"]
        ac = state["acceptance_criteria"]
        

        historical_context = self._build_context(docs)
        precondition = state.get("channel_setup", {}).get(channel, "")
        logger.info(f"\n Precondition for {channel}:\n{precondition}\n")

        logger.info(f"🤖 Generating testcase for channel: {channel}")

        # Safeguard for missing 'channel_rules'
        channel_rules = state.get("channel_rules", {})
        if channel not in channel_rules:
            logger.warning(f"Missing 'channel_rules' for channel: {channel}")
            channel_rules[channel] = ""  # Default to an empty string or appropriate fallback value

        result = self.chain.invoke(
            {
                "user_story_id": state["user_story_id"],
                "user_story": user_story,
                "description": description,
                "ac": ac,
                "historical_context": historical_context,
                "precondition": precondition,
                "channel_rules": channel_rules[channel]
            }
        )

        logger.info(f" LLM output received for {channel}")

        return result.content

    # ---------------------------------------------------------
    # LangGraph Node Entry
    # ---------------------------------------------------------
    def run(self, state: RAGState) -> RAGState:
        logger.info(" LLM Testcase Generator Agent started")

        llm_outputs = {}

        for channel, docs in state["retrieved_docs"].items():
            if not docs:
                logger.warning(f" No docs for channel {channel}")
                continue

            llm_text = self._generate_for_channel(state, channel, docs)
            llm_outputs[channel] = llm_text

        state["llm_outputs"] = llm_outputs

        logger.info(" LLM generation completed for all channels")
        return state
