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
