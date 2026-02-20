import logging
import json
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.channel_detector import detect_channels
from utils.state_debugger import dump_state_to_txt
from config.config import get
from utils.channel_rules import build_channel_rules

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:
    """
    Responsibilities:
    1. Fetch User Story from ADO
    2. Clean Description HTML + download images
    3. Clean Acceptance Criteria HTML + download images
    4. Detect Channels
    5. 🔥 Split story into channel-specific meaning
    6. Prepare state for retrieval agent
    """

    def __init__(self):
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )
        self.model = get("CHAT_MODEL")

    # ---------------------------------------------------------
    # Channel Context Derivation (MOST IMPORTANT PART)
    # ---------------------------------------------------------
    def _derive_channel_context(self, description: str, ac: str, channels):

        logger.info("🧠 Deriving channel specific flows from story")

        prompt = f"""
You are a mortgage workflow analyst.

Your job:
Split the story into workflow meaning for each mortgage channel.

Channel definitions:
RTL = Loan officer + borrower interaction
WHL = Broker submits package / broker compliance
DTC = Borrower self-service portal behavior
CL1 = Correspondent purchase / post-closing workflow

Rules:
• A story may contain mixed workflows
• Extract only relevant sentences per channel
• DO NOT copy entire story to every channel
• If nothing relevant → return empty string
• Be conservative (precision > recall)

DESCRIPTION:
{description}

AC:
{ac}

Return STRICT JSON:

{{
"RTL": "...",
"WHL": "...",
"DTC": "...",
"CL1": "..."
}}
"""

        try:
            resp = self.openai.chat.completions.create(
                model=self.model,
                messages=[{"role": "user", "content": prompt}],
                temperature=0
            )

            content = resp.choices[0].message.content.strip()
            parsed = json.loads(content)

            # ensure all channels exist
            for ch in channels:
                parsed.setdefault(ch, "")

            logger.info(f"✅ Channel context derived: {parsed}")
            return parsed

        except Exception as e:
            logger.warning(f"⚠️ LLM context split failed → fallback rule mode: {e}")

            # fallback: give full story but still safe
            full_text = description + "\n" + ac
            return {ch: full_text for ch in channels}

    # ---------------------------------------------------------
    # MAIN ENTRY
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:
        logger.info("🚀 ADO Intelligence Agent started")

        user_story_id = state["user_story_id"]

        # 1️⃣ Fetch
        story = fetch_from_ado(user_story_id)

        # 2️⃣ Clean HTML
        logger.info("🧹 Processing Description HTML + Images...")
        description_enriched = process_html_and_download_images(
            story["description"], user_story_id, "description"
        )

        logger.info("🧹 Processing Acceptance Criteria HTML + Images...")
        ac_enriched = process_html_and_download_images(
            story["acceptance_criteria"], user_story_id, "ac"
        )

        # 3️⃣ Detect channels
        channels = detect_channels(ac_enriched)
        channel_rules_map ={
            ch: build_channel_rules(ch) for ch in channels
        }
        logger.info(f"✅ Channels detected: {channels}")

        # 🔥 4️⃣ NEW — derive channel context
        channel_context_map = self._derive_channel_context(
            description_enriched,
            ac_enriched,
            channels
        )

        # Debug output
        print("\n=========== ADO INTELLIGENCE AGENT OUTPUT ===========\n")
        print(f"User Story ID: {user_story_id}")
        print("TITLE:", story["title"])
        logger.info(f"CHANNEL CONTEXT MAP:\n{json.dumps(channel_context_map, indent=2)}")
        print("\n=====================================================\n")

        dump_state_to_txt({
            "user_story_id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
            "channels": channels,
            "channel_context_map": channel_context_map
        })

        # -------------------------------------------------
        # STATE MUTATION
        # -------------------------------------------------
        state["user_story"] = story["title"]
        state["description"] = description_enriched
        state["acceptance_criteria"] = ac_enriched
        state["channels"] = channels
        state["channel_rules_map"] = channel_rules_map

        # 🔥 NEW DATA FOR RETRIEVAL AGENT
        state["channel_context_map"] = channel_context_map

        state["story"] = {
            "id": user_story_id,
            "title": story["title"],
            "description": description_enriched,
            "acceptance_criteria": ac_enriched,
        }

        return state
------------------------------------------------------------
import logging
from typing import Dict, List

from azure.search.documents import SearchClient
from azure.search.documents.models import VectorizedQuery
from azure.core.credentials import AzureKeyCredential
from openai import AzureOpenAI

from config.config import get

logger = logging.getLogger(__name__)


class RetrievalIntelligenceAgent:

    # =========================================================
    # INIT
    # =========================================================
    def __init__(self):

        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )

        self.embed_model = get("EMBEDDING_MODEL")
        self.chat_model = get("CHAT_MODEL")

        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY")),
        )

    # =========================================================
    # EMBEDDING
    # =========================================================
    def _embed(self, text: str) -> List[float]:
        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=text[:8000]  # token safety
        )
        return emb.data[0].embedding

    # =========================================================
    # HYBRID VECTOR SEARCH
    # =========================================================
    def _vector_retrieve(self, query_text: str, channel: str, topk: int):

        vector_query = VectorizedQuery(
            vector=self._embed(query_text),
            fields="embedding",
            k_nearest_neighbors=topk
        )

        filter_query = f"channels/any(c: c eq '{channel}')"

        results = self.search_client.search(
            search_text=query_text,
            vector_queries=[vector_query],
            filter=filter_query,
            select=["testCaseId", "content"],
            top=topk
        )

        return list(results)

    # =========================================================
    # RERANK TESTCASES
    # =========================================================
    def _rerank_testcases(self, query_text: str, docs: List[Dict]):

        if not docs:
            return []

        combined = ""
        for idx, d in enumerate(docs, start=1):
            combined += f"""
Document {idx}
TestCaseId: {d.get('testCaseId')}
Content:
{d.get('content')}
---------------------
"""

        prompt = f"""
Rank relevance to this workflow:

{query_text}

Return ordered TestCaseId only.
{combined}
"""

        resp = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        ranking = resp.choices[0].message.content.strip().splitlines()
        id_map = {d["testCaseId"]: d for d in docs if d.get("testCaseId")}

        return [id_map[x] for x in ranking if x in id_map][:10]

    # =========================================================
    # SETUP INFERENCE (CHANNEL AWARE RAG)
    # =========================================================
    def _infer_setup(self, channel: str, channel_text: str, knowledge_docs: List[Dict]):

        knowledge_context = "\n".join(d.get("content","") for d in knowledge_docs[:6])

        prompt = f"""
You are a mortgage domain expert.

Infer a REALISTIC loan setup for THIS channel workflow only.

Channel: {channel}

Workflow Meaning:
{channel_text}

System Knowledge:
{knowledge_context}

Return ONLY:

Loan Purpose:
Loan Type:
Product:
Loan Stage:
Existing Conditions:
"""

        resp = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        return resp.choices[0].message.content.strip()

    # =========================================================
    # BUILD CONTEXT PER CHANNEL
    # =========================================================
    def _build_channel_context(self, channel_text: str, channel: str):

        logger.info(f"🔎 Retrieval for {channel} using channel workflow")

        tests = self._vector_retrieve(channel_text, channel, 40)
        reranked_tests = self._rerank_testcases(channel_text, tests)

        setup = self._infer_setup(channel, channel_text, reranked_tests)

        logger.info(f"\n Inferred Setup for {channel}:\n{setup}\n")

        return {
            "tests": reranked_tests,
            "setup": setup,
            "flow": [],
            "rules": [],
            "guidelines": []
        }

    # =========================================================
    # LANGGRAPH ENTRY
    # =========================================================
    def run(self, state: Dict) -> Dict:

        logger.info("🚀 Retrieval Intelligence Agent (Channel Aware RAG)")

        retrieved_docs = {}

        # 🔥 CORRECT KEY
        channel_contexts = state.get("channel_context_map", {})

        for channel in state["channels"]:

            channel_text = channel_contexts.get(channel, "").strip()

            # smarter fallback (not full story)
            if len(channel_text) < 20:
                logger.warning(f"⚠️ Weak context for {channel}, using title only")
                channel_text = state["user_story"]

            retrieved_docs[channel] = self._build_channel_context(channel_text, channel)

        state["retrieved_docs"] = retrieved_docs
        state["channel_setup"] = {ch: retrieved_docs[ch]["setup"] for ch in retrieved_docs}

        logger.info(f"\n final channel setups: {state['channel_setup']} \n")

        return state
-----------------------------------------------------------------
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
--------------------------------------------------------------------------------------

import logging
import os
import re
from typing import Dict

from openpyxl import load_workbook
from config.config import get
from utils.product_mapper import resolve_product_code
from utils.loan_domain_normalizer import normalize_full_setup

logger = logging.getLogger(__name__)


class ExcelExportAgent:

    def __init__(self):
        self.template_path = get("EXCEL_TEMPLATE_PATH")
        self.output_dir = get("EXCEL_OUTPUT_DIR")

    # ---------------------------------------------------------
    # Parse raw LLM text into structured testcase
    # ---------------------------------------------------------
    def _parse_llm_output(self, llm_text: str) -> Dict:
        scenario = ""
        script = ""
        requirement = ""
        steps = []
        step_counter = 1

        GENERIC_WORDS = ["action", "verify", "check", "navigate", "enter", "select"]

        for raw in llm_text.splitlines():
            line = raw.strip()

            if not line:
                continue

            # ---------------- headers ----------------
            if line.lower().startswith("scenario:") and not scenario:
                scenario = line.split(":", 1)[1].strip()
                continue

            if line.lower().startswith("script:") and not script:
                script = line.split(":", 1)[1].strip()
                continue

            if line.lower().startswith("requirement:") and not requirement:
                requirement = line.split(":", 1)[1].strip()
                continue

            # ---------------- steps ----------------
            if re.match(r"^step\s*\d+", line.lower()):

                # remove "Step 01"
                cleaned = re.sub(r"^step\s*\d+\s*", "", line, flags=re.IGNORECASE).strip()

                # split pipe or legacy format
                if "|" in cleaned:
                    parts = [p.strip() for p in cleaned.split("|")]
                else:
                    parts = [cleaned]

                # remove empties
                parts = [p for p in parts if p]

                if not parts:
                    continue

                # ---------------- intelligent column mapping ----------------
                if len(parts) >= 4:

                    first = parts[0].lower()

                    # LLM inserted verb column → shift left
                    if first in GENERIC_WORDS:
                        desc = parts[1]
                        screen = parts[2] if len(parts) > 2 else "NA"
                        data = parts[3] if len(parts) > 3 else "NA"
                        expected = parts[4] if len(parts) > 4 else "Verify system behavior"
                    else:
                        desc = parts[0]
                        screen = parts[1] if len(parts) > 1 else "NA"
                        data = parts[2] if len(parts) > 2 else "NA"
                        expected = parts[3] if len(parts) > 3 else "Verify system behavior"

                elif len(parts) == 3:
                    desc, screen, data = parts
                    expected = "Verify system behavior"

                elif len(parts) == 2:
                    desc, screen = parts
                    data = "NA"
                    expected = "Verify system behavior"

                else:
                    desc = parts[0]
                    screen = "NA"
                    data = "NA"
                    expected = "Verify system behavior"

                steps.append({
                    "step_no": f"Step {step_counter:02d}",
                    "desc": desc,
                    "screen": screen,
                    "data": data,
                    "expected": expected,
                })

                step_counter += 1

        return {
            "scenario": scenario,
            "script": script,
            "requirement": requirement,
            "steps": steps
        }

    # ---------------------------------------------------------
    # Convert inferred setup -> Template precondition
    # ---------------------------------------------------------
    def _format_precondition(self, channel: str, setup_text: str) -> str:

        if not setup_text:
            logger.warning(f" No setup found for {channel}, using fallback")
            return f"Channel: {channel}"

        # 🔥 USE NORMALIZED STRUCTURE
        data = normalize_full_setup(channel, setup_text)

        loan_purpose = data["loan_purpose"]
        loan_type = data["loan_type"]
        loan_stage = data["loan_stage"]

        product_code = resolve_product_code(
            loan_type=loan_type,
            channel=channel
        )

        portal_map = {
            "RTL": "Customer Portal",
            "WHL": "Broker Portal",
            "CL1": "Broker Portal",
            "DTC": "Ignite Portal"
        }

        portal = portal_map.get(channel, "Portal")

        formatted = f"""Create a loan from {portal} as per pre-conditions below:
1. Channel: {channel}
2. Loan Purpose: {loan_purpose}
3. Loan Type: {loan_type}
4. Product Code: {product_code}
5. Loan Stage: {loan_stage}"""

        return formatted

    # ---------------------------------------------------------
    def _write_testcase(self, ws, start_row, tc_id, tc_data, precondition):
        row = start_row

        for idx, step in enumerate(tc_data["steps"]):
            ws.cell(row, 1).value = tc_id if idx == 0 else ""
            ws.cell(row, 2).value = tc_data["script"] if idx == 0 else ""
            ws.cell(row, 3).value = "NA" if idx == 0 else ""
            ws.cell(row, 4).value = tc_data["scenario"] if idx == 0 else ""
            ws.cell(row, 5).value = precondition if idx == 0 else ""

            ws.cell(row, 6).value = step["step_no"]
            ws.cell(row, 7).value = step["desc"]
            ws.cell(row, 8).value = step["screen"]
            ws.cell(row, 9).value = step["data"]
            ws.cell(row, 10).value = step["expected"]

            ws.cell(row, 11).value = tc_data["requirement"] if idx == 0 else ""

            row += 1

        return row

    # ---------------------------------------------------------
    # LangGraph entry
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:
        logger.info(" Excel Export Agent started")

        os.makedirs(self.output_dir, exist_ok=True)
        wb = load_workbook(self.template_path)

        story_channels = state.get("channels", [])
        setup_map = state.get("channel_setup", {})
        llm_outputs = state.get("llm_outputs", {})

        logger.info(f"Detected channels: {story_channels}")
        logger.info(f"Incoming setup_map keys: {list(setup_map.keys())}")

        # ---------------------------------------------------------
        # REMOVE UNUSED SHEETS
        # ---------------------------------------------------------
        for sheet in list(wb.sheetnames):
            if sheet not in story_channels:
                logger.info(f" Removing unused sheet: {sheet}")
                std = wb[sheet]
                wb.remove(std)

        # Ensure channel sheet exists (safety)
        for ch in story_channels:
            if ch not in wb.sheetnames:
                logger.info(f" Creating missing sheet: {ch}")
                wb.create_sheet(ch)

        # ---------------------------------------------------------
        # Dynamic trackers (ONLY for active channels)
        # ---------------------------------------------------------
        sheets = {name: wb[name] for name in wb.sheetnames}
        row_tracker = {ch: 2 for ch in story_channels}
        tc_counter = {ch: 1 for ch in story_channels}

        user_story_id = state["user_story_id"]

        # ---------------------------------------------------------
        # WRITE TESTCASES
        # ---------------------------------------------------------
        for channel in story_channels:

            llm_text = llm_outputs.get(channel)
            if not llm_text:
                logger.warning(f" No LLM output for {channel}, skipping")
                continue

            ws = sheets[channel]

            tc_data = self._parse_llm_output(llm_text)

            row = row_tracker[channel]
            tc_id = f"US_{user_story_id}_TC_{tc_counter[channel]:02d}"

            setup_text = setup_map.get(channel, "")
            precondition = self._format_precondition(channel, setup_text)

            logger.info(f"\nFormatted precondition for {channel}:\n{precondition}\n")

            new_row = self._write_testcase(ws, row, tc_id, tc_data, precondition)

            row_tracker[channel] = new_row
            tc_counter[channel] += 1

        # ---------------------------------------------------------
        # SAVE
        # ---------------------------------------------------------
        output_file = os.path.join(
            self.output_dir,
            f"Indiv_US_{user_story_id}_Test Scripts_v1.0.xlsx"
        )

        wb.save(output_file)
        logger.info(f" Excel generated: {output_file}")

        state["excel_output"] = output_file
        return state
---------------------------------------------------------------
# llm_factory.py
from openai import AzureOpenAI
from config.config import get

def get_openai_client():
    return AzureOpenAI(
        api_key=get("AZURE_OPENAI_API_KEY"),
        api_base=get("AZURE_OPENAI_ENDPOINT"),
        api_version=get("AZURE_OPENAI_API_VERSION"),
        
    )
    
def get_chat_model():
    return get("CHAT_MODEL")


def get_embedding_model():
    return get("EMBEDDING_MODEL")    
---------------------------------------------------------------------
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from config.config import get


def get_search_client():
    return SearchClient(
        endpoint=get("AZURE_SEARCH_ENDPOINT"),
        index_name=get("AZURE_SEARCH_INDEX_NAME"),
        credential=AzureKeyCredential(get("AZURE_SEARCH_API_KEY"))
    )
    
---------------------------------------------------------------------------
# graph/graph_builder.py

from langgraph.graph import StateGraph, END

from state.rag_state import RAGState

from agents.ado_intelligence_agent import ADOIntelligenceAgent
from agents.retrieval_intelligence_agent import RetrievalIntelligenceAgent
from agents.llm_testcase_generator_agent import LLMTestcaseGeneratorAgent
from agents.excel_export_agent import ExcelExportAgent



def build_graph():

    graph = StateGraph(RAGState)

    # Initialize agents
    ado_agent = ADOIntelligenceAgent()
    retrieval_agent = RetrievalIntelligenceAgent()
    llm_agent = LLMTestcaseGeneratorAgent()
    excel_agent = ExcelExportAgent()
    

    # Add nodes
    graph.add_node("ado_agent", ado_agent.run)
    graph.add_node("retrieval_agent", retrieval_agent.run)
    graph.add_node("llm_agent", llm_agent.run)
    graph.add_node("excel_agent", excel_agent.run)
     

    # Define flow
    graph.set_entry_point("ado_agent")

    graph.add_edge("ado_agent", "retrieval_agent")
    graph.add_edge("retrieval_agent", "llm_agent")
    graph.add_edge("llm_agent", "excel_agent")
    graph.add_edge("excel_agent", END)

    return graph.compile()
-----------------------------------------------------

from typing import TypedDict, Dict, List


class RAGState(TypedDict, total=False):
    user_story_id: str

    # ADO Agent outputs
    user_story: str
    description: str
    acceptance_criteria: str
    channels: List[str]
    preconditions: Dict[str, str]
    story: Dict

    # Retrieval Agent outputs
    retrieved_docs: Dict[str, List[Dict]]
    
    channel_setup: Dict[str, str]
    
 

    # LLM Agent outputs
    llm_outputs: Dict[str, str]

    # Excel Agent outputs
    excel_output: str

-------------------------------------------------------
# utils/channel_detector.py
import re
import logging

logger = logging.getLogger(__name__)

ALL_CHANNELS = ["RTL", "WHL", "DTC", "CL1"]


# -------------------------------------------------
# Behavioral Channel Detection
# -------------------------------------------------
def detect_channels(text: str) -> list:

    logger.info("Behavioral channel detection started...")

    t = text.upper()

    # ---------------------------
    # 1. Persona Detection (strongest signal)
    # ---------------------------
    if "NON-BROKER USER IN H2O" in t or "INTERNAL USER" in t:
        logger.info("Detected INTERNAL H2O user → WHL")
        return ["WHL"]

    if "BROKER PORTAL" in t or "BROKER LO" in t:
        logger.info("Detected Broker persona → WHL")
        return ["WHL"]

    if "CUSTOMER PORTAL" in t or "BORROWER" in t:
        logger.info("Detected Borrower persona → RTL")
        return ["RTL"]

    if "IGNITE" in t or "DIRECT TO CONSUMER" in t:
        logger.info("Detected Ignite flow → DTC")
        return ["DTC"]

    if "CORRESPONDENT" in t or "CL1" in t:
        logger.info("Detected Correspondent → CL1")
        return ["CL1"]

    # ---------------------------
    # 2. Feature based detection
    # ---------------------------
    if "BUSINESS UNIT" in t or "CREATE LOAN ON BEHALF OF" in t:
        logger.info("Detected internal operations feature → WHL")
        return ["WHL"]

    # ---------------------------
    # Fallback
    # ---------------------------
    logger.info("No strong signal → using ALL channels")
    return ALL_CHANNELS
---------------------------------------------------
def build_channel_rules(channel: str) -> str:

    rules = {
        "RTL": """
Retail channel rules:
- Loan officer + borrower interaction only
- NO broker entities exist
- NO correspondent purchase flow
- Broker compensation fields must NOT appear
- Validate consumer disclosure behavior only
""",

        "WHL": """
Wholesale channel rules:
- Broker originated loan
- Broker compensation and license validations apply
- Submission package and broker docs expected
""",

        "DTC": """
DTC channel rules:
- Pure self-service borrower workflow
- No loan officer and no broker interaction
- Automated disclosure generation expected
""",

        "CL1": """
Correspondent channel rules:
- Loan already originated externally
- Purchase advice and acquisition validations apply
- No borrower application workflow
"""
    }

    return rules.get(channel, "")
--------------------------------------------------------------------------
import re

# =========================================================
# 1. PRODUCT MAPPING (Domain Truth)
# =========================================================

LOAN_TYPE_PRODUCT_MAP = {
    "CONVENTIONAL": "CF30",
    "CONVENTIONAL JUMBO": "JCPF30",
    "FHA": "FF30",
    "VA": "VF30",
    "USDA": "UF30",
    "HELOC": "NRZHeloc",
    "NON QM": "NRSEF30",
    "SECOND LIEN": "SASF30A"
}


# ---------------------------------------------------------
# Robust extractor (handles LLM formatting noise)
# ---------------------------------------------------------
def _extract(label: str, text: str) -> str:
    if not text:
        return ""

    patterns = [
        rf"{label}\s*:\s*(.*)",
        rf"{label}\s*-\s*(.*)",
        rf"\*\*{label}\*\*\s*:\s*(.*)",
    ]

    for p in patterns:
        m = re.search(p, text, re.IGNORECASE)
        if m:
            return m.group(1).strip()

    return ""


# =========================================================
# 2. LOAN TYPE NORMALIZATION
# =========================================================

def normalize_loan_type(raw: str) -> str:
    if not raw:
        return "Conventional"

    raw_upper = raw.upper()

    for key in LOAN_TYPE_PRODUCT_MAP:
        if key in raw_upper:
            return key.title()

    return "Conventional"


def resolve_product_code(loan_type: str) -> str:
    key = loan_type.upper()
    return LOAN_TYPE_PRODUCT_MAP.get(key, "CF30")


# =========================================================
# 3. STAGE NORMALIZATION
# =========================================================

CHANNEL_STAGES = {

    "RTL": [
        "Application Accepted","In-Processing","UW Submitted",
        "Approved W/Conditions","Conditions Submitted",
        "Conditions In Review","Final Approval In Review",
        "Clear To Close","Closing Disclosure Ordered",
        "Closing Disclosure Sent","Closing Docs sent",
        "Funds Ordered","Funds Sent","Funds Released"
    ],

    "WHL": [
        "Created","LE Sent","Submission Review","UW Submitted",
        "Approved W/Conditions","Conditions Submitted",
        "Conditions In Review","Final Approval In Review",
        "Clear To Close","Closing Disclosure Ordered",
        "Closing Disclosure Sent","Closing Docs sent",
        "Funds Ordered","Funds Sent","Funds Released"
    ],

    "DTC": [
        "Application Accepted","CD Audit Submitted","CD Audit Completed",
        "UW Submitted","Approved W/Conditions","Conditions Submitted",
        "Conditions In Review","Final Approval In Review",
        "Clear To Close","Closing Disclosure Ordered",
        "Closing Disclosure Sent","Closing Docs sent",
        "Funds Ordered","Funds Sent","Funds Released"
    ],

    "CL1": [
        "Created","UW Submitted","Approved W/Conditions",
        "Conditions Submitted","Conditions In Review",
        "Final Approval In Review","Clear To Close",
        "Closing Disclosure Ordered","Closing Disclosure Sent",
        "Closing Docs sent","Correspondent Funded",
        "Loan Purchase Review","Purchase Wire Review",
        "Approved For Purchase","Funds Released"
    ]
}


STAGE_SYNONYMS = {
    "approved with conditions": "Approved W/Conditions",
    "approval with conditions": "Approved W/Conditions",
    "generate disclosures": "Closing Disclosure Ordered",
    "disclosure generation": "Closing Disclosure Ordered",
    "cd generated": "Closing Disclosure Sent",
    "ctc": "Clear To Close",
}


def normalize_stage(channel: str, raw_stage: str) -> str:

    if not raw_stage:
        return CHANNEL_STAGES[channel][0]

    stage = raw_stage.lower()

    # handle ranges like "Application Accepted through CD Audit Completed"
    if "through" in stage or "to" in stage:
        parts = re.split(r"through|to", stage)
        stage = parts[-1].strip()

    # synonym mapping
    for key, value in STAGE_SYNONYMS.items():
        if key in stage:
            return value

    # fuzzy match allowed stages
    for allowed in CHANNEL_STAGES[channel]:
        if allowed.lower() in stage:
            return allowed

    return CHANNEL_STAGES[channel][0]


# =========================================================
# 4. PURPOSE NORMALIZATION
# =========================================================

def normalize_purpose(raw: str) -> str:

    if not raw:
        return "Purchase"

    raw = raw.lower()

    if "refi" in raw:
        return "Refinance"
    if "construction" in raw:
        return "Construction Permanent"

    return "Purchase"


# =========================================================
# MAIN ENTRY — CONTRACT GUARANTEED STRUCTURE
# =========================================================

def normalize_full_setup(channel: str, setup_text: str) -> dict:

    purpose_raw = _extract("Loan Purpose", setup_text)
    type_raw = _extract("Loan Type", setup_text)
    stage_raw = _extract("Loan Stage", setup_text)

    purpose = normalize_purpose(purpose_raw)
    loan_type = normalize_loan_type(type_raw)
    product = resolve_product_code(loan_type)
    stage = normalize_stage(channel, stage_raw)

    # IMPORTANT: standardized keys used by Excel agent
    return {
        "loan_purpose": purpose,
        "loan_type": loan_type,
        "product_code": product,
        "loan_stage": stage
    }
------------------------------------------------------------------------------------------------
# utils/product_mapper.py

# ---------------------------------------------------------
# Product Rules (Domain Accurate)
# ---------------------------------------------------------

PRIMARY_PRODUCTS = {
    "CONVENTIONAL": ["CF30", "CF15", "CF10"],
    "CONVENTIONAL JUMBO": ["JCPF30", "JEG10A", "JF30B"],
    "FHA": ["FF30"],
    "VA": ["VF30", "VF15"],
    "USDA": ["CF30"],
    "HELOC": ["NRZHeloc"],
    "NON QM": ["NRSEF30", "NRSVF30"],
}

SECOND_LIEN_PRODUCTS = ["SASF30A", "SASF30B", "SASF30C"]

HELOC_ALLOWED_CHANNELS = ["RTL", "DTC"]


# ---------------------------------------------------------
# Resolver
# ---------------------------------------------------------

def resolve_product_code(
    loan_type: str,
    channel: str = None,
    lien_type: str = "First Lien",
    doc_type: str = "Full"
) -> str:
    """
    Smart deterministic product resolver.
    Prevents impossible combinations.
    """

    if not loan_type:
        return "CF30"

    lt = loan_type.upper().strip()
    lien = (lien_type or "").upper()

    # -----------------------------
    # Second lien overrides all
    # -----------------------------
    if "SECOND" in lien:
        return SECOND_LIEN_PRODUCTS[0]

    # -----------------------------
    # HELOC restrictions
    # -----------------------------
    if "HELOC" in lt:
        if channel in HELOC_ALLOWED_CHANNELS:
            return "NRZHeloc"
        else:
            return "CF30"  # fallback safe loan

    # -----------------------------
    # Standard mapping
    # -----------------------------
    for key in PRIMARY_PRODUCTS:
        if key in lt:
            return PRIMARY_PRODUCTS[key][0]

    # -----------------------------
    # Safe default
    # -----------------------------
    return "CF30"
-----------------------------------------------------------------------
# Senior Mortgage QA Analyst Prompt

## Role and Responsibility

You are a Senior Mortgage QA Analyst.

Your goal is to verify the mortgage system behaves correctly and cannot behave incorrectly.

You validate:
- Business rules
- Decision outcomes
- Lifecycle state transitions
- Data dependencies
- Recovery behavior

Think like a real manual tester executing an end-to-end scenario.

Channel Behavior Constraints:
{channel_rules}

STRICT RULE:
You MUST NOT create steps violating channel behavior.
If a field does not exist in this channel → do not test it.


## Internal Analysis (Do Not Output)

Before generating the test case, determine:
- What business decision is being validated
- What condition controls that decision
- What should happen when valid
- What must NOT happen when invalid
- What system state must remain unchanged during failure
- What recovery behavior must occur after correction

Design the test so it FAILS if the logic is wrong.

## Execution Pattern Rule (VERY IMPORTANT)

Follow the execution structure and style observed in the historical test cases:
- Learn step style from historical tests
- Maintain similar level of detail
- Maintain similar starting behavior (login / open loan / direct validation) ONLY if seen in history
- Do NOT artificially insert steps
- Do NOT omit common steps if history consistently uses them

The structure must feel written by the same QA team.

### Continuous Flow Rule (Critical)

Generate ONE continuous test scenario. Do NOT create multiple test cases for small variations.

Within the SAME flow include:
- Valid condition → system progresses
- Boundary condition → system evaluated
- Invalid or missing data → system blocked
- Stage must not change on failure
- Correct the data
- System recovers and progresses

Everything must happen in one lifecycle journey.

### State Machine Validation Rule

The mortgage system behaves as a lifecycle engine. Your test must verify:
- Allowed stage transitions occur
- Forbidden transitions are blocked
- Stage remains unchanged on validation failure
- Stage changes only after correction

Prefer validating system state instead of UI messages.

### Dependency Validation Rule

When applicable, validate dependent data behavior. Examples:
- Product affects eligibility
- Loan purpose affects disclosures
- Missing fields prevent progression
- Conflicting data blocks movement
- Correction restores eligibility

At least one conflicting combination must be tested.

## Test Design Rules

Focus on SYSTEM behavior — not navigation. Avoid writing steps like:
- Page loads
- User clicks tab
- Message displayed

Prefer writing:
- Loan moved to underwriting
- Conditions generated
- Progression blocked
- Previous stage retained
- Recalculation occurred

### Data Intelligence Rule

Test data must influence the system. Use:
- Boundary values
- Conflicting data
- Missing required data
- Corrected data

Do NOT use generic words like "valid" or "invalid".

## Output Rules (STRICT)

- Generate EXACTLY ONE test case
- Do NOT include preconditions
- Use pipe `|` separated format
- Steps must be sequential (Step 01, Step 02…)
- Expected results must describe SYSTEM behavior
- No explanations outside the format
- Continue until final correct state is reached
- Follow historical testcase writing style

### Output Format

```
Scenario: <business validation scenario>
Script: <short functional name>
Requirement: <requirement mapping>

Step 01 | Description | Screen | Data | Expected system behavior
Step 02 | Description | Screen | Data | Expected system behavior
Step 03 | Description | Screen | Data | Expected system behavior
...
```

## Contextual Information

- **User Story ID:** {user_story_id}
- **User Story:** {user_story}
- **Description:** {description}
- **Acceptance Criteria:** {ac}

### Realistic System Setup Before Test:
{precondition}

### Historical Knowledge (Primary Behavioral Reference)

Use the following real historical test cases as behavioral reference:
{historical_context}

Generate the test case now.


	
