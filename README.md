import logging
import base64
from bs4 import BeautifulSoup
from openai import AzureOpenAI

from ado.ado_client import fetch_from_ado
from utils.html_image_processor import process_html_and_download_images
from utils.step_generator import convert_json_to_grouped_steps
from utils.channel_detector import detect_channels
from utils.output_writer import save_final_txt
from utils.image_optimizer import resize_image_if_needed
from config.config import get

logger = logging.getLogger(__name__)


class ADOIntelligenceAgent:

    def __init__(self):
        self.client = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )
        self.model = get("CHAT_MODEL")

    # ---------------------------------------------------------
    # Encode image to Base64
    # ---------------------------------------------------------
    def _encode_image(self, path: str) -> str:
        try:
            with open(path, "rb") as f:
                return base64.b64encode(f.read()).decode("utf-8")
        except Exception as e:
            logger.error(f"Failed to encode image {path}: {e}")
            return ""

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("ADO Intelligence Agent started")

        story_id = state["user_story_id"]

        # -----------------------------------------------------
        # 1️⃣ Fetch ADO Work Item
        # -----------------------------------------------------
        story = fetch_from_ado(story_id)
        logger.info("ADO story fetched successfully")

        raw_description = story.get("description", "")
        raw_ac = story.get("acceptance_criteria", "")

        # -----------------------------------------------------
        # 2️⃣ Clean DESCRIPTION (TEXT ONLY)
        # -----------------------------------------------------
        soup_desc = BeautifulSoup(raw_description, "html.parser")
        for tag in soup_desc(["script", "style"]):
            tag.decompose()

        clean_description = soup_desc.get_text(separator="\n").strip()
        logger.info("Description cleaned (no images downloaded)")

        # -----------------------------------------------------
        # 3️⃣ Process ACCEPTANCE CRITERIA (Download Images)
        # -----------------------------------------------------
        ac_data = process_html_and_download_images(
            raw_ac, story_id, "acceptance_criteria"
        )

        clean_ac = ac_data["clean_text"]
        ac_images = ac_data["image_paths"]

        if not ac_images:
            logger.warning("No Acceptance Criteria images found.")
            logger.info("Proceeding with text-only GPT extraction.")
        else:
            logger.info(f"AC Images downloaded: {len(ac_images)}")

        # -----------------------------------------------------
        # 4️⃣ Build Vision Input (AC Images Only)
        # -----------------------------------------------------
        vision_content = [{
            "type": "text",
            "text": f"""
User Story:
{clean_description}

Acceptance Criteria:
{clean_ac}
"""
        }]

        for img_path in ac_images:

            #  Resize before sending
            optimized_path = resize_image_if_needed(img_path)

            encoded_image = self._encode_image(optimized_path)

            if encoded_image:
                vision_content.append({
                    "type": "image_url",
                    "image_url": {
                        "url": f"data:image/jpeg;base64,{encoded_image}"
                    }
                })

        logger.info("Sending request to Azure GPT-4o")

        # -----------------------------------------------------
        # 5️⃣ Call Azure GPT-4o (Force JSON)
        # -----------------------------------------------------
        response = self.client.chat.completions.create(
            model=self.model,
            temperature=0,
            response_format={"type": "json_object"},
            messages=[
                {
                    "role": "system",
                    "content": """
You are a Senior Mortgage QA Analyst.

Extract structured JSON strictly in this format:

{
  "fields": [
    {
      "name": "",
      "type": "",
      "values": [],
      "location": "",
      "visibilityRule": "",
      "privilegeRestricted": false
    }
  ]
}

Return JSON only.
"""
                },
                {
                    "role": "user",
                    "content": vision_content
                }
            ]
        )

        logger.info(" GPT-4o response received")

        llm_output = response.choices[0].message.content

        # -----------------------------------------------------
        # 6️⃣ Convert JSON → Grouped Acceptance Criteria
        # -----------------------------------------------------
        grouped_steps = convert_json_to_grouped_steps(llm_output)
        logger.info(" Structured Acceptance Criteria generated")

        # -----------------------------------------------------
        # 7️⃣ Detect Channels from RAW AC
        # -----------------------------------------------------
        channels = detect_channels(clean_ac)
        logger.info(f" Channels detected from RAW AC: {channels}")

        # -----------------------------------------------------
        # 8️⃣ Save Output File
        # -----------------------------------------------------
        save_final_txt(
            story_id,
            story.get("title"),
            clean_description,
            grouped_steps
        )

        # -----------------------------------------------------
        # 9️⃣ Update State (For Retrieval Agent)
        # -----------------------------------------------------
        state["story_id"] = story_id
        state["title"] = story.get("title")
        state["user_story"] = story.get("title")
        state["description"] = clean_description

        # Raw AC (kept for reference)
        state["original_acceptance_criteria"] = clean_ac

        # Final Acceptance Criteria (LLM Generated)
        state["acceptance_criteria"] = grouped_steps

        state["llm_output"] = llm_output
        state["channels"] = channels

        logger.info(f"Final state keys: {list(state.keys())}")
        logger.info("ADO Intelligence Agent completed successfully")

        return state
--------------------------------------------------------------------------------------------
import logging
from typing import Dict, List

from azure.search.documents import SearchClient
from azure.search.documents.models import VectorizedQuery
from azure.core.credentials import AzureKeyCredential
from openai import AzureOpenAI
from config.config import get

logger = logging.getLogger(__name__)


class RetrievalIntelligenceAgent:

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

    # ---------------------------------------------------------
    # Embed Query
    # ---------------------------------------------------------
    def _embed(self, text: str) -> List[float]:

        response = self.openai.embeddings.create(
            model=self.embed_model,
            input=text[:8000]
        )

        return response.data[0].embedding

    # ---------------------------------------------------------
    # Hybrid Search
    # ---------------------------------------------------------
    def _hybrid_search(self, query_text: str, channel: str, topk: int = 20):

        vector_query = VectorizedQuery(
            vector=self._embed(query_text),
            fields="embedding",
            k_nearest_neighbors=topk
        )

        filter_query = f"channels/any(c: c eq '{channel}')"

        results = list(self.search_client.search(
            search_text=query_text,
            vector_queries=[vector_query],
            filter=filter_query,
            select=["testCaseId", "content"],
            top=topk
        ))

        logger.info(f"{channel} → Retrieved {len(results)} docs")

        return results

    # ---------------------------------------------------------
    # Robust Precondition Extraction
    # ---------------------------------------------------------
    def _extract_precondition(self, content: str) -> str:

        lines = content.splitlines()
        capture = False
        collected = []

        for line in lines:

            lower = line.lower().strip()

            # START capture
            if (
                "pre-condition" in lower or
                "precondition" in lower or
                "pre condition" in lower
            ):
                capture = True
                collected.append(line)
                continue

            # STOP capture
            if capture and (
                lower.startswith("step") or
                "test steps" in lower or
                "=========== test steps" in lower
            ):
                break

            if capture:
                collected.append(line)

        result = "\n".join(collected).strip()
        if result.lower().startswith("pre-condition")or result.lower().startswith("precondition") or result.lower().startswith("pre condition"): 
            lines = result.splitlines()
            result = "\n".join(lines[1:]).strip()
        return result   

    # ---------------------------------------------------------
    # LLM Rerank
    # ---------------------------------------------------------
    def _rerank(self, story_text: str, docs: List[Dict]) -> List[Dict]:

        if not docs:
            return []

        combined = ""
        for idx, d in enumerate(docs, 1):
            combined += f"\nDoc {idx}\n{d.get('content')[:1500]}\n"

        prompt = f"""
Rank the below documents by relevance to this story.
Return only numbers in order separated by space.

Story:
{story_text}

Documents:
{combined}
"""

        response = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        ranking_text = response.choices[0].message.content.strip()
        ranking_tokens = ranking_text.split()

        ordered_docs = []

        for token in ranking_tokens:
            if token.isdigit():
                idx = int(token) - 1
                if 0 <= idx < len(docs):
                    ordered_docs.append(docs[idx])

        return ordered_docs if ordered_docs else docs

    # ---------------------------------------------------------
    # Main Execution
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Retrieval Agent Running")

        full_story = f"""
User Story: {state['user_story']}
Description: {state['description']}
Acceptance Criteria: {state['acceptance_criteria']}
"""

        channel_context = {}
        selected_preconditions = {}

        for channel in state["channels"]:

            docs = self._hybrid_search(full_story, channel)
            reranked_docs = self._rerank(full_story, docs)

            best_precondition = ""
            historical_steps = ""

            # Try to extract precondition from reranked docs
            for doc in reranked_docs:

                content = doc.get("content", "")

                if not best_precondition:
                    extracted = self._extract_precondition(content)
                    if extracted:
                        best_precondition = extracted

                historical_steps += "\n" + content[:1000]

                if best_precondition:
                    break

            #  Fallback if nothing found
            if not best_precondition and reranked_docs:
                logger.warning(f"{channel} → No precondition found. Using first doc fallback.")
                best_precondition = "Precondition not found in historical data."

            channel_context[channel] = {
                "precondition": best_precondition,
                "historical_steps": historical_steps[:4000]
            }

            selected_preconditions[channel] = best_precondition

            # logger.info(
            #     f"{channel} → Selected Precondition:\n{best_precondition}\n"
            # )

        #  Store BOTH maps in state
        state["channel_context"] = channel_context
        state["selected_preconditions"] = selected_preconditions

        # logger.info("Selected Preconditions Map:")
        # logger.info(selected_preconditions)

        logger.info(" Retrieval Completed")

        return state
---------------------------------------------------------------------------------------
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

        logger.info("LLM Testcase Generator initialized")

    # ---------------------------------------------------------
    # LangGraph Entry
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("LLM Generator Running")

        # clone full state safely
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

        #  DO NOT TOUCH selected_preconditions
        # Just preserve whatever came from retrieval

        return new_state

--------------------------------------------------------------------------------------
import os
import logging
from openpyxl import load_workbook
from config.config import get

logger = logging.getLogger(__name__)


class ExcelExportAgent:

    def __init__(self):
        self.template_path = get("EXCEL_TEMPLATE_PATH")
        self.output_dir = get("EXCEL_OUTPUT_DIR")

    # -------------------------------------------------
    # STRICT PIPE FORMAT PARSER (NO REGEX)
    # -------------------------------------------------
    def _parse_llm_output(self, llm_text: str):

        scenario = ""
        script = ""
        requirement = ""
        steps = []

        lines = llm_text.splitlines()

        for line in lines:
            line = line.strip()

            if not line:
                continue

            if line.startswith("Test Scenario Description:") or line.startswith("Scenario:"):
                scenario = line.replace("Test Scenario Description:", "").replace("Scenario:", "").strip()
                continue

            if line.startswith("Script:") or line.startswith("Test Script Description:"):
                script = line.replace("Script:", "").replace("Test Script Description:", "").strip()
                continue

            if line.startswith("Test Scenario Id:"):
                requirement = line.replace("Test Scenario Id:", "").strip()
                continue

            if line.lower().startswith("step") and "|" in line:

                parts = [p.strip() for p in line.split("|")]

                if len(parts) != 6:
                    continue

                steps.append({
                    "step_no": parts[0],
                    "desc": parts[1],
                    "screen": parts[2],
                    "data": parts[3],
                    "expected": parts[4],
                    "requirement": parts[5]
                })

        return {
            "scenario": scenario,
            "script": script,
            "requirement": requirement,
            "steps": steps
        }

    # -------------------------------------------------
    # MAIN EXECUTION
    # -------------------------------------------------
    def run(self, state: dict) -> dict:

        logger.info("Excel Export Agent started")

        os.makedirs(self.output_dir, exist_ok=True)

        wb = load_workbook(self.template_path)

        # -------------------------------------------------
        #  DELETE UNUSED SHEETS BASED ON CHANNEL DETECTION
        # -------------------------------------------------
        detected_channels = state.get("channels", [])

        if detected_channels:
            for sheet_name in list(wb.sheetnames):
                if sheet_name not in detected_channels:
                    wb.remove(wb[sheet_name])

        logger.info(f"Sheets after cleanup: {wb.sheetnames}")

        user_story_id = state["user_story_id"]

        # -------------------------------------------------
        # PROCESS EACH CHANNEL
        # -------------------------------------------------
        for channel, llm_text in state["llm_outputs"].items():

            if channel not in wb.sheetnames:
                logger.warning(f"Sheet '{channel}' not found after cleanup.")
                continue

            ws = wb[channel]
            row = 2

            #  Reset counter per channel
            tc_counter = 1

            parsed = self._parse_llm_output(llm_text)

            # logger.info(f"{channel} -> Parsed {len(parsed['steps'])} steps")

            if not parsed["steps"]:
                continue

            selected_preconditions = state.get("selected_preconditions", {})
            precondition = selected_preconditions.get(channel, "")

            tc_id = f"US_{user_story_id}_{channel}_TC_{tc_counter:02d}"

            start_row = row  # for merging

            for idx, step in enumerate(parsed["steps"]):

                ws.cell(row, 1).value = tc_id if idx == 0 else ""
                ws.cell(row, 2).value = f"{user_story_id}-{channel}" if idx == 0 else ""
                ws.cell(row, 3).value = parsed["scenario"] if idx == 0 else ""
                ws.cell(row, 4).value = parsed["script"] if idx == 0 else ""
                ws.cell(row, 5).value = precondition if idx == 0 else ""

                ws.cell(row, 6).value = step["step_no"]
                ws.cell(row, 7).value = step["desc"]
                ws.cell(row, 8).value = step["screen"]
                ws.cell(row, 9).value = step["data"]
                ws.cell(row, 10).value = step["expected"]

                ws.cell(row, 11).value = ""
                ws.cell(row, 12).value = ""
                ws.cell(row, 13).value = ""
                ws.cell(row, 14).value = ""
                ws.cell(row, 15).value = parsed["requirement"] 

                row += 1

            end_row = row - 1

            # -------------------------------------------------
            # 🔥 MERGE TEST SCENARIO ID COLUMN (Column 2)
            # -------------------------------------------------
            if end_row > start_row:
                ws.merge_cells(start_row=start_row, start_column=2,
                                end_row=end_row, end_column=2)

            tc_counter += 1

        output_file = os.path.join(
            self.output_dir,
            f"Indiv_US_{user_story_id}_Test_Scripts_v1.0.xlsx"
        )

        wb.save(output_file)

        logger.info(f"Excel generated: {output_file}")

        state["excel_output"] = output_file
        return state
