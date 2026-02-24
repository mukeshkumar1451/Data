# run_agent.py

import logging

from graph.graph_builder import build_graph

logging.basicConfig(level=logging.INFO)


def run(user_story_id: str):
    app = build_graph()

    initial_state = {        "user_story_id": user_story_id
    }

    final_state = app.invoke(initial_state)

    print("\n Excel Generated at:")
    print(final_state["excel_output"])


if __name__ == "__main__":
    # Example run
    run("718521")
