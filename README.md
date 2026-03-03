import logging
import json
import os
from typing import Dict, List
from datetime import datetime

from azure.search.documents import SearchClient
from azure.search.documents.models import VectorizedQuery
from azure.core.credentials import AzureKeyCredential
from openai import AzureOpenAI
from config.config import get

logger = logging.getLogger(__name__)


class RetrievalIntelligenceAgent:

    def __init__(self):

        # Azure OpenAI
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION"),
        )

        self.embed_model = get("EMBEDDING_MODEL")
        self.chat_model = get("CHAT_MODEL")

        # Azure Search
        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY")),
        )

        # Log directory
        self.log_dir = get("RETRIEVAL_LOG_DIR") or "./retrieval_logs"
        os.makedirs(self.log_dir, exist_ok=True)

    # ---------------------------------------------------------
    # Embed Title
    # ---------------------------------------------------------
    def _embed(self, text: str) -> List[float]:

        response = self.openai.embeddings.create(
            model=self.embed_model,
            input=text[:8000]
        )
        return response.data[0].embedding

    # ---------------------------------------------------------
    # Build Dynamic Keyword OR Query (No Hardcoding)
    # ---------------------------------------------------------
    def _build_keyword_query(self, title: str) -> str:

        tokens = [
            word.strip()
            for word in title.replace(">", " ")
                             .replace("-", " ")
                             .replace("/", " ")
                             .split()
            if len(word.strip()) > 2
        ]

        # Remove duplicates dynamically
        unique_tokens = list(dict.fromkeys(tokens))

        if not unique_tokens:
            return title

        return " OR ".join(unique_tokens)

    # ---------------------------------------------------------
    # Hybrid Search (Vector + Keyword)
    # ---------------------------------------------------------
    def _hybrid_search(self, title: str, channel: str, topk: int = 10):

        search_query = self._build_keyword_query(title)

        vector_query = VectorizedQuery(
            vector=self._embed(title),
            fields="embedding",
            k_nearest_neighbors=topk
        )

        filter_query = f"channels/any(c: c eq '{channel}')"

        results = list(
            self.search_client.search(
                search_text=search_query,
                vector_queries=[vector_query],
                filter=filter_query,
                select=["testCaseId", "content"],
                top=topk
            )
        )

        return search_query, results

    # ---------------------------------------------------------
    # Rule-Based Flow Pattern Detection (Generic)
    # ---------------------------------------------------------
    def _detect_flow_patterns(self, text: str) -> Dict:

        lower = text.lower()

        return {
            "audit_behavioral_pattern":
                lower.count("audit") > 1 and
                lower.count("previous") > 0 and
                lower.count("new") > 0,

            "requires_save_cycle":
                lower.count("save") > 0,

            "value_transition_pattern":
                lower.count("yes") > 0 and lower.count("no") > 0,

            "has_checkbox_pattern":
                lower.count("checkbox") > 0,

            "has_dropdown_pattern":
                lower.count("dropdown") > 0
        }

    # ---------------------------------------------------------
    # Single LLM Extraction (Structure Only)
    # ---------------------------------------------------------
    def _extract_summary(self, combined_content: str) -> Dict:

        prompt = f"""
Extract structured JSON:

{{
  "precondition": "",
  "historical_scenarios": [],
  "historical_scripts": [],
  "step_patterns": []
}}

Return JSON only.

Content:
{combined_content[:12000]}
"""

        response = self.openai.chat.completions.create(
            model=self.chat_model,
            temperature=0,
            response_format={"type": "json_object"},
            messages=[
                {"role": "system", "content": "You extract QA structural patterns."},
                {"role": "user", "content": prompt}
            ]
        )

        try:
            return json.loads(response.choices[0].message.content)
        except Exception:
            return {
                "precondition": "",
                "historical_scenarios": [],
                "historical_scripts": [],
                "step_patterns": []
            }

    # ---------------------------------------------------------
    # Save Retrieval Log (Full Transparency)
    # ---------------------------------------------------------
    def _save_retrieval_log(
        self,
        story_id: str,
        channel: str,
        title: str,
        search_query: str,
        results: List[Dict],
        flow_intelligence: Dict
    ):

        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")

        log_file = os.path.join(
            self.log_dir,
            f"retrieval_{story_id}_{channel}_{timestamp}.json"
        )

        log_data = {
            "story_id": story_id,
            "channel": channel,
            "title_used_for_search": title,
            "search_query_used": search_query,
            "retrieved_count": len(results),
            "retrieved_documents": [
                {
                    "testCaseId": doc.get("testCaseId"),
                    "content_preview": doc.get("content", "")[:2000]
                }
                for doc in results
            ],
            "flow_intelligence": flow_intelligence
        }

        with open(log_file, "w", encoding="utf-8") as f:
            json.dump(log_data, f, indent=2)

        logger.info(f"Retrieval log saved: {log_file}")

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Retrieval Intelligence Agent Running")

        title = state["user_story"]
        story_id = state["user_story_id"]

        channel_context = {}

        for channel in state["channels"]:

            logger.info(f"Processing channel: {channel}")

            search_query, docs = self._hybrid_search(title, channel)

            if not docs:
                logger.warning(f"No documents retrieved for {channel}")
                continue

            combined_content = "\n\n".join(
                [doc.get("content", "")[:4000] for doc in docs]
            )

            flow_intelligence = self._detect_flow_patterns(combined_content)

            structured = self._extract_summary(combined_content)

            channel_context[channel] = {
                "precondition": structured.get("precondition", ""),
                "historical_scenarios": structured.get("historical_scenarios", [])[:3],
                "historical_scripts": structured.get("historical_scripts", [])[:3],
                "historical_steps": structured.get("step_patterns", [])[:30],
                "flow_intelligence": flow_intelligence
            }

            # Save detailed retrieval log
            self._save_retrieval_log(
                story_id,
                channel,
                title,
                search_query,
                docs,
                flow_intelligence
            )

        state["channel_context"] = channel_context

        logger.info("Retrieval Intelligence Agent Completed")
        return state
========================================================================================
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

    # ---------------------------------------------------------
    # BUILD INTELLIGENT MULTI-CLUSTER PROMPT
    # ---------------------------------------------------------
    def _build_prompt(self, payload: Dict) -> str:

        return f"""
You are a Senior Mortgage QA Analyst generating structured enterprise LOS test cases.

============================================================
ARCHITECTURE PRINCIPLE
============================================================

1. Acceptance Criteria defines WHAT must be validated.
2. Historical Workflow Clusters define HOW validation is typically performed.
3. Historical Precondition Candidates define setup structure.
4. You must intelligently combine these.
5. Do NOT copy Acceptance Criteria sentences.
6. Do NOT invent setup values.
7. Do NOT hardcode behavior.

============================================================
WORKFLOW CLUSTERS (INSTITUTIONAL MEMORY)
============================================================

{payload["workflow_clusters"]}

Instructions:
- Analyze all clusters.
- Select cluster(s) that align with validation intent.
- Preserve full ordered sequence of selected cluster.
- If cluster contains modify → save → audit → previous/new validation,
  apply entire behavioral chain.
- Do NOT partially apply cluster in a way that breaks logical order.
- Do NOT apply unrelated cluster patterns.
============================================================
MANDATORY BEHAVIORAL APPLICATION RULE
=================================================================
If Acceptance Criteria introduces:
 - Editable dropdown
 - Editable checkbox
 - Editable field
And a workflow cluster contains:

 - Field modification
 - Save action
 - Audit validation of previous and new values

You MUST apply that full behavioral sequence.

Static render-only validation is invalid when behavioral workflow exists.

============================================================
PRECONDITION CANDIDATES
============================================================

{payload["precondition_candidates"]}

Instructions:
- Select the precondition aligned with chosen workflow cluster.
- Prefer preconditions referencing relevant functional area (e.g., DIS, Disclosure, Audit).
- Preserve structured numbered list format.
- Do NOT merge multiple unrelated preconditions.
- Do NOT invent environment URLs or product codes.

============================================================
CHANNEL ENFORCEMENT
============================================================

Channel: {payload["channel"]}

If channel is RTL or DTC:
- Mortgage Broker entities do not exist.
- Do NOT generate broker validation.
- Do NOT mention broker fields.
- Ignore broker-related AC content entirely.

If channel is WHL or CL1:
- Include broker validations only if present in Acceptance Criteria.

============================================================
AC TRANSFORMATION RULE
============================================================

Acceptance Criteria:
{payload["ac"]}

- Extract validation intent only.
- Transform into behavioral enterprise test steps.
- Do NOT start steps with "Verify".
- Do NOT copy AC wording.
- Do NOT produce static-only validation when workflow implies behavioral sequence.

============================================================
SCENARIO & SCRIPT DESCRIPTION RULES
============================================================

Test Scenario Description:
- One sentence.
- Business objective only.
- Max 25 words.
- No technical details.
- No AC sentence reuse.

Test Script Description:
- 2–3 sentences.
- Summarize validation coverage.
- Mention workflow behavior if applicable.
- Do NOT restate AC.

============================================================
CONTEXT
============================================================

User Story:
{payload["user_story"]}

Description:
{payload["description"]}

============================================================
OUTPUT FORMAT (STRICT)
============================================================

Test Case ID / Test Script ID: {payload["user_story_id"]}_{payload["channel"]}_01
Test Scenario Id: {payload["user_story_id"]}_SC_01
Test Scenario Description: <one sentence>
Test Script Description: <2-3 sentences>
Pre-Condition & Assumptions:
<structured numbered precondition>

Then steps strictly as:

Step XX | Description | Screen Name | Test Data | Expected Result | Requirement Mapping

Rules:
- Step 01: Login
- Step 02: Open Loan
- Business steps
- Last step: Logout
- One validation per step
- Expected Result must begin with "The system"
- No markdown
- No notes
- No explanations
- No extra commentary

Generate complete enterprise-grade test case now.
"""

    # ---------------------------------------------------------
    # MAIN EXECUTION
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("LLM Intelligent Testcase Generation Started")

        outputs = {}

        for channel, ctx in state["channel_context"].items():

            payload = {
                "user_story_id": state["user_story_id"],
                "user_story": state["user_story"],
                "description": state["description"],
                "ac": state["acceptance_criteria"],
                "channel": channel,
                "workflow_clusters": ctx.get("workflow_clusters", []),
                "precondition_candidates": ctx.get("precondition_candidates", [])
            }

            prompt = self._build_prompt(payload)

            response = self.llm.invoke(prompt)

            outputs[channel] = response.content.strip()

        state["llm_outputs"] = outputs

        logger.info("LLM Intelligent Testcase Generation Completed")

        return state
