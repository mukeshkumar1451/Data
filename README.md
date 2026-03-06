from langgraph.graph import StateGraph, END

from state.rag_state import RAGState

from agents.ado_intelligence_agent import ADOIntelligenceAgent
from agents.retrieval_intelligence_agent import RetrievalIntelligenceAgent
from agents.llm_testcase_generator_agent import LLMTestcaseGeneratorAgent
from agents.review_agent import ReviewAgent
from agents.excel_export_agent import ExcelExportAgent


def build_graph():

    graph = StateGraph(RAGState)

    # Initialize agents
    ado_agent = ADOIntelligenceAgent()
    retrieval_agent = RetrievalIntelligenceAgent()
    llm_agent = LLMTestcaseGeneratorAgent()
    review_agent = ReviewAgent()
    excel_agent = ExcelExportAgent()

    # -----------------------------
    # Nodes
    # -----------------------------
    graph.add_node("ado_agent", ado_agent.run)
    graph.add_node("retrieval_agent", retrieval_agent.run)
    graph.add_node("llm_agent", llm_agent.run)
    graph.add_node("review_agent", review_agent.run)
    graph.add_node("excel_agent", excel_agent.run)

    # -----------------------------
    # Flow
    # -----------------------------
    graph.set_entry_point("ado_agent")

    graph.add_edge("ado_agent", "retrieval_agent")
    graph.add_edge("retrieval_agent", "llm_agent")

    # NEW QUALITY GATE
    graph.add_edge("llm_agent", "review_agent")

    graph.add_edge("review_agent", "excel_agent")
    graph.add_edge("excel_agent", END)

    return graph.compile()
    =================================================-------===============================
    from typing import TypedDict, Dict, List


class HistoricalContext(TypedDict, total=False):
    precondition: str
    historical_scenario: str
    historical_script: str
    historical_steps: List[Dict[str, str]]


class ReviewResult(TypedDict, total=False):
    missing_keywords: List[str]
    review_status: str
    attempts: int


class RAGState(TypedDict, total=False):

    # -----------------------------------
    # Input
    # -----------------------------------
    user_story_id: str

    # -----------------------------------
    # ADO Agent Outputs
    # -----------------------------------
    title: str
    user_story: str
    description: str
    acceptance_criteria: str
    channels: List[str]
    story: Dict

    # -----------------------------------
    # Retrieval Agent Outputs
    # -----------------------------------
    retrieved_docs: Dict[str, List[Dict]]
    channel_context: Dict[str, HistoricalContext]

    # -----------------------------------
    # LLM Agent Outputs
    # -----------------------------------
    llm_outputs: Dict[str, str]

    # -----------------------------------
    # Review Agent Outputs
    # -----------------------------------
    review_results: Dict[str, ReviewResult]

    # -----------------------------------
    # Excel Export Outputs
    # -----------------------------------
    excel_output: str
