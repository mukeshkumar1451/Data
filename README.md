
import logging
from typing import Dict

from langchain_openai import AzureChatOpenAI
from langchain_core.prompts import PromptTemplate
from config.config import get

logger = logging.getLogger(__name__)


class LLMTestcaseGeneratorAgent:

    def __init__(self):

        self.llm = AzureChatOpenAI(
            azure_deployment=get("CHAT_MODEL"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_key=get("AZURE_OPENAI_KEY"),
            temperature=0.2,
        )

        self.prompt = PromptTemplate(
            input_variables=[
                "user_story",
                "description",
                "ac",
                "historical_tests",
                "channel"
            ],
            template="""
You are a QA automation expert.

User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}

Channel:
{channel}

Historical Testcases:
{historical_tests}

IMPORTANT:

You MUST generate precondition using EXACT template below.
Do NOT change wording.
Do NOT add extra bullets.

If Channel = RTL:

Create a loan from Customer Portal as per pre-conditions below:
1. Channel: RTL
2. Loan Purpose:
3. Loan Type:
4. Product Code:
5. Loan Stage:

If Channel = WHL:

Create a loan from Broker Portal as per pre-conditions below:
1. Channel: Wholesale
2. Loan Purpose:
3. Loan Type:
4. Product Code:
5. Loan Stage:

If Channel = DTC:

Create a loan from Ignite Portal as per pre-conditions below:
1. Channel: DTC
2. Loan Purpose:
3. Loan Type:
4. Product Code:
5. Loan Stage:

If Channel = CL1:

Create a loan from Broker Portal as per pre-conditions below:
1. Channel: CL1
2. Loan Purpose:
3. Loan Type:
4. Product Code:
5. Loan Stage:

After precondition, generate structured test steps.
"""
        )

        self.chain = self.prompt | self.llm

    # -------------------------------------------------
    # Build historical context
    # -------------------------------------------------
    def _build_historical_text(self, contexts):

        text = ""

        for c in contexts:
            text += "\n--- Historical Testcase ---\n"
            text += c["retrieved_steps"][:1000]

        return text[:6000]

    # -------------------------------------------------
    # Main Entry
    # -------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("🤖 LLM Generator Running")

        outputs = {}

        for channel, contexts in state["channel_context"].items():

            historical_text = self._build_historical_text(contexts)

            payload = {
                "user_story": state["user_story"],
                "description": state["description"],
                "ac": state["acceptance_criteria"],
                "historical_tests": historical_text,
                "channel": channel
            }

            result = self.chain.invoke(payload)

            outputs[channel] = result.content

            print("\n===== GENERATED OUTPUT =====\n")
            print(result.content)

        state["llm_outputs"] = outputs

        logger.info("✅ LLM Generation Completed")
        return state
        
-------------------------------
prompt location file PROMPT_TEMPLATE_PATH=prompts/testcase_prompts.txt
