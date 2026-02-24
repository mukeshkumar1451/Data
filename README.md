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

    # 🔥 ADD THIS
    channel_context: Dict[str, List[Dict]]

    channel_setup: Dict[str, str]

    # LLM Agent outputs
    llm_outputs: Dict[str, str]

    # Excel Agent outputs
    excel_output: str
