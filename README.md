import os
from datetime import datetime


def dump_state_to_txt(state: dict, filename: str = "ado_agent_{user_story_id}_output.txt"):
    os.makedirs("debug", exist_ok=True)

    filename = filename.format(
        user_story_id=state.get("user_story_id", "unknown")
    )

    path = os.path.join("debug", filename)

    with open(path, "w", encoding="utf-8") as f:
        f.write("=========== ADO INTELLIGENCE AGENT OUTPUT ===========\n\n")

        f.write(f"User Story ID:\n{state.get('user_story_id')}\n\n")

        f.write("----------- TITLE -----------\n")
        f.write(f"{state.get('title')}\n\n")

        f.write("----------- ENRICHED DESCRIPTION -----------\n")
        f.write(f"{state.get('description')}\n\n")

        f.write("----------- CHANNELS -----------\n")
        f.write(f"{state.get('channels')}\n\n")

        f.write("----------- ENRICHED ACCEPTANCE CRITERIA -----------\n")
        f.write(f"{state.get('acceptance_criteria')}\n\n")

        f.write("----------- PRECONDITIONS -----------\n")
        for ch, pre in state.get("preconditions", {}).items():
            f.write(f"\n[{ch}]\n{pre}\n")

    print(f"\n State dumped to: {path}\n")
