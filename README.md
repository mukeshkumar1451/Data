# rag_query.py
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from azure.search.documents.models import VectorizedQuery

import logging
from openai import AzureOpenAI
from embeddingtovectordb.config import get
from ContextRetrieval_ReRanking.prompts.prompt_templates import build_testcase_prompt
from ContextRetrieval_ReRanking.rerankerbase.reranker import LLMReranker

logger = logging.getLogger(__name__)


class TestCaseRAGRetriever:

    def __init__(self):

        # -------- Azure OpenAI --------
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION")
        )

        self.chat_model = get("CHAT_MODEL")
        self.embed_model = get("EMBEDDING_MODEL")
        self.top_k = get("TOP_K", int)

        # -------- LLM Reranker --------
        self.reranker = LLMReranker(self.openai, self.chat_model)

        # -------- Azure AI Search --------
        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
        )

    # ----------------------------------------------------
    # Create embedding
    # ----------------------------------------------------
    def embed_query(self, text):
        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=text
        )
        return emb.data[0].embedding

    # ----------------------------------------------------
    # Hybrid Search + Re-ranking for ONE channel
    # ----------------------------------------------------
    def retrieve_for_channel(self, user_story, description, ac, channel):
        logger.info(f"\n🔎 Hybrid search for channel: {channel}")

        query_text = f"""
User Story:
{user_story}

Description:
{description}

Acceptance Criteria:
{ac}
"""

        query_vector = self.embed_query(query_text)

        vector_query = VectorizedQuery(
            kind="vector",
            vector=query_vector,
            k=self.top_k,
            fields="embedding"
        )

        # ✅ HYBRID SEARCH (text + vector)
        results = self.search_client.search(
            search_text=query_text,
            vector_queries=[vector_query],
            filter=f"channel eq '{channel}'",
            select=["testCaseId", "content", "channel"],
            top=self.top_k
        )

        results_list = list(results)

        logger.info(f"✅ Retrieved {len(results_list)} testcases before re-ranking")

        for r in results_list[:5]:
            score = r.get('@search.score', 0)
            logger.info(f"   📊 Vector Score: {score:.4f} | TC: {r['testCaseId']}")

        # ---------------- Re-ranking ----------------
        reranked = self.reranker.rerank(query_text, results_list)

        logger.info("\n🔁 After LLM Re-ranking:\n")
        for r in reranked[:10]:
            logger.info(
                f"   🦬 Rerank: {r['rerank_score']:.3f} | "
                f"Vector: {r.get('@search.score', 0):.4f} | "
                f"TC: {r['testCaseId']}"
            )

        return reranked

    # ----------------------------------------------------
    # Build historical context (no chunkId anymore)
    # ----------------------------------------------------
    def _build_historical_context(self, results):

        historical_context = ""

        for r in results:
            tcid = r["testCaseId"]
            content = r["content"]

            historical_context += (
                f"\n\n### Historical TestCase: {tcid}\n{content}\n"
            )

        return historical_context

    # ----------------------------------------------------
    # Generate testcase using LLM for this channel
    # ----------------------------------------------------
    def generate_testcase_with_llm(
        self,
        user_story_id,
        user_story,
        description,
        ac,
        retrieved_chunks,
    ):

        if not retrieved_chunks:
            logger.warning("⚠️ No historical testcases → skipping LLM")
            return {}

        channel = retrieved_chunks[0]["channel"]

        historical_context = self._build_historical_context(retrieved_chunks)

        prompt = build_testcase_prompt(
            user_story_id=user_story_id,
            user_story=user_story,
            description=description,
            ac=ac,
            historical_context=historical_context
        )

        logger.info(f"🤖 Sending {channel} context to Azure OpenAI...\n")

        response = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[
                {"role": "system", "content": "You are a QA Test Case Designer."},
                {"role": "user", "content": prompt}
            ],
            temperature=0.2
        )

        output = response.choices[0].message.content

        logger.info(f"✅ LLM response received for {channel}\n")

        return {channel: output}

----------------------------------------------------------------------------------
# test_rag_runner.py
# -*- coding: utf-8 -*-
import os
import sys
import traceback
import logging

sys.path.insert(0, os.path.dirname(os.path.dirname(os.path.abspath(__file__))))

logger = logging.getLogger(__name__)

from ContextRetrieval_ReRanking.ragquery.rag_query import TestCaseRAGRetriever as RAGRetriever
from ContextRetrieval_ReRanking.llm.llm_step_parser import parse_llm_steps
from ContextRetrieval_ReRanking.excelexport.excel_multi_sheet_exporter import MultiSheetExcelExporter
from embeddingtovectordb.config import get
from ContextRetrieval_ReRanking.channel_detect.channel_detector import detect_channels


def run_rag_pipeline(user_story_id, user_story, description, ac) -> str:
    try:
        logger.info("🚀 Channel-Aware RAG Test Case Generation Started")

        channels = detect_channels(ac)
        logger.info(f"Channels detected: {channels}")

        retriever = RAGRetriever()

        all_generated_testcases = []

        for channel in channels:
            logger.info(f"Processing Channel: {channel}")

            results = retriever.retrieve_for_channel(
                user_story,
                description,
                ac,
                channel
            )

            llm_outputs = retriever.generate_testcase_with_llm(
                user_story_id=user_story_id,
                user_story=user_story,
                description=description,
                ac=ac,
                retrieved_chunks=results
            )

            parsed = parse_llm_steps(llm_outputs[channel])

            for tc in parsed:
                tc["channels"] = [channel]

            all_generated_testcases.extend(parsed)

        template_path = get("EXCEL_TEMPLATE_PATH")
        output_dir = get("EXCEL_OUTPUT_DIR")
        os.makedirs(output_dir, exist_ok=True)

        output_file = os.path.join(
            output_dir,
            f"Indiv_US_{user_story_id}_Test Scripts_v1.0.xlsx"
        )

        exporter = MultiSheetExcelExporter(template_path)
        exporter.export(
            testcases=all_generated_testcases,
            user_story_id=user_story_id,
            output_path=output_file
        )

        logger.info(f"Excel generated: {output_file}")
        return output_file

    except Exception:
        logger.exception("ERROR OCCURRED IN RAG PIPELINE")
        raise

------------------------------------------------------------------
# reranker.py
class LLMReranker:

    def __init__(self, openai, model):
        self.openai = openai
        self.model = model

    def rerank(self, query_text, results):

        scored = []

        for r in results:
            chunk_text = r["content"]

            prompt = f"""
Rate how relevant this historical test step is for the given user story.

User Story:
{query_text}

Historical Chunk:
{chunk_text}

Give ONLY a number between 0 and 1.
"""

            resp = self.openai.chat.completions.create(
                model=self.model,
                messages=[{"role": "user", "content": prompt}],
                temperature=0
            )

            try:
                score = float(resp.choices[0].message.content.strip())
            except:
                score = 0.0

            r["rerank_score"] = score
            scored.append(r)

        # filter below 0.5
        filtered = [x for x in scored if x["rerank_score"] >= 0.5]

        # sort best first
        filtered.sort(key=lambda x: x["rerank_score"], reverse=True)

        return filtered[:12]
------------------------------------------
# server.py
# -*- coding: utf-8 -*-
from mcp.server import FastMCP
import json
import logging
import os
import sys
from dotenv import load_dotenv

from ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_images

# -------------------------------------------------------------------
# ✅ ENV-CONTROLLED MCP LOGGING (ROOT LOGGER)
# -------------------------------------------------------------------

load_dotenv()

LOG_LEVEL = os.getenv("LOG_LEVEL", "INFO").upper()

level_map = {
    "DEBUG": logging.DEBUG,
    "INFO": logging.INFO,
    "WARNING": logging.WARNING,
    "ERROR": logging.ERROR,
}

LOGGING_LEVEL = level_map.get(LOG_LEVEL, logging.INFO)

sys.stdout.reconfigure(line_buffering=True)

log_dir = os.path.join(os.path.dirname(os.path.dirname(__file__)), "logs")
os.makedirs(log_dir, exist_ok=True)
log_file = os.path.join(log_dir, "mcp_server.log")

root_logger = logging.getLogger()
root_logger.setLevel(LOGGING_LEVEL)

# Remove handlers added by MCP runtime
for h in root_logger.handlers[:]:
    root_logger.removeHandler(h)

formatter = logging.Formatter(
    "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
)

file_handler = logging.FileHandler(log_file, encoding="utf-8")
file_handler.setFormatter(formatter)

console_handler = logging.StreamHandler(sys.stdout)
console_handler.setFormatter(formatter)

root_logger.addHandler(file_handler)
root_logger.addHandler(console_handler)

logger = logging.getLogger(__name__)
logger.info(f"✅ MCP Logging initialized with level: {LOG_LEVEL}")

# -------------------------------------------------------------------
# MCP SERVER
# -------------------------------------------------------------------

mcp = FastMCP("ADO User Story Server")


@mcp.tool()
def get_user_story(user_story_id: str):
    logger.info(f"Fetching user story: {user_story_id}")

    try:
        story = fetch_from_ado(user_story_id)

        if not all(k in story for k in ["title", "description", "acceptance_criteria"]):
            raise KeyError("Missing required keys in ADO response")

        clean_ac = process_html_and_images(story["acceptance_criteria"])

        result = {
            "user_story_id": user_story_id,
            "title": story["title"],
            "description": story["description"],
            "acceptance_criteria": clean_ac,
        }

        logger.info(f"Successfully processed user story {user_story_id}")
        return json.dumps(result, indent=2)

    except Exception:
        logger.exception(f"Error in get_user_story for {user_story_id}")
        raise


@mcp.tool()
def us_TestcaseGenerator(user_story_id: str):
    logger.info(f"TEST CASE GENERATION STARTED FOR: {user_story_id}")

    try:
        story = fetch_from_ado(user_story_id)
        clean_ac = process_html_and_images(story["acceptance_criteria"])

        from test_rag_runner import run_rag_pipeline

        output_excel = run_rag_pipeline(
            user_story_id=user_story_id,
            user_story=story["title"],
            description=story["description"],
            ac=clean_ac,
        )

        logger.info(f"Test cases generated: {output_excel}")
        return f"Test cases generated: {output_excel}"

    except Exception:
        logger.exception("Error during test case generation")
        raise


if __name__ == "__main__":
    logger.info("[*] MCP Server starting...")
    logger.info("[*] Available tools: get_user_story, us_TestcaseGenerator")
    mcp.run()
-------------------------------------------------
# test_rag.py
import os
import traceback
import yaml
import sys
import logging

logger = logging.getLogger(__name__)

from ContextRetrieval_ReRanking.ragquery.rag_query import TestCaseRAGRetriever as RAGRetriever
from ContextRetrieval_ReRanking.llm.llm_step_parser import parse_llm_steps
from ContextRetrieval_ReRanking.excelexport.excel_multi_sheet_exporter import MultiSheetExcelExporter
from embeddingtovectordb.config import get
from ContextRetrieval_ReRanking.channel_detect.channel_detector import detect_channels
from adomcpserver.mcp_client import MCPClient



def load_userstory(path: str):
    logger.info("📥 Loading user story YAML...")
    with open(path, "r", encoding="utf-8") as f:
        data = yaml.safe_load(f)
    logger.info("✅ YAML loaded")
    return data


if __name__ == "__main__":
    try:
        logger.info("\n🚀 TRUE Channel-Aware RAG Test Case Generation Started\n")

        # ---------------------------------------------------
        # Step 1 — Load User Story
        # ---------------------------------------------------
        if "--ado" in sys.argv:
            story_id= sys.argv[sys.argv.index("--ado") + 1]
            mcp=MCPClient()
            story = mcp.get_enriched_user_story(story_id)
        else:
            
            story = load_userstory("userstory_input.yaml")

        user_story_id = story["user_story_id"]
        user_story = story["user_story"]
        description = story["description"]
        ac = story["acceptance_criteria"]

        # ---------------------------------------------------
        # Step 2 — Detect Channels from AC
        # ---------------------------------------------------
        channels = detect_channels(ac)
        logger.info(f"\n🔎 Channels detected: {channels}\n")

        # ---------------------------------------------------
        # Step 3 — Initialize Retriever
        # ---------------------------------------------------
        retriever = RAGRetriever()

        all_generated_testcases = []

        # ---------------------------------------------------
        # Step 4 — PROCESS EACH CHANNEL SEPARATELY (CORE FIX)
        # ---------------------------------------------------
        for channel in channels:

            logger.info(f"\n==============================")
            logger.info(f"🔷 Processing Channel: {channel}")
            logger.info(f"==============================\n")

            # 🔍 Vector search only for this channel
            results = retriever.retrieve_for_channel(
                user_story,
                description,
                ac,
                channel
            )

            logger.info(f"✅ Retrieved {len(results)} chunks for {channel}\n")

            # 🤖 Generate testcase using this channel history
            llm_outputs = retriever.generate_testcase_with_llm(
                user_story_id=user_story_id,
                user_story=user_story,
                description=description,
                ac=ac,
                retrieved_chunks=results
            )

            llm_text = llm_outputs[channel]

            logger.info("🧩 Parsing LLM response...\n")
            parsed = parse_llm_steps(llm_text)

            for tc in parsed:
                tc["channels"] = [channel]

            all_generated_testcases.extend(parsed)

        # ---------------------------------------------------
        # Step 5 — Export to Excel
        # ---------------------------------------------------
        logger.info("\n📄 Writing channel-specific testcases into Excel template...\n")

        template_path = get("EXCEL_TEMPLATE_PATH")
        output_dir = get("EXCEL_OUTPUT_DIR")
        os.makedirs(output_dir, exist_ok=True)

        output_file = os.path.join(
            output_dir,
            f"Indiv_US_{user_story_id}_Test Scripts_v1.0.xlsx"
        )

        exporter = MultiSheetExcelExporter(template_path)
        exporter.export(
            testcases=all_generated_testcases,
            user_story_id=user_story_id,
            output_path=output_file
        )

        logger.info(f"\n🎉 Excel generated successfully:\n{output_file}\n")

    except Exception as e:
        logger.error("\n❌ ERROR OCCURRED")
        logger.error(e)
        logger.error("\n📌 TRACEBACK:\n")
        traceback.print_exc()


