import os
import json
from datetime import datetime


def save_llm_output_to_txt(
    story_id: str,
    title: str,
    description: str,
    acceptance_criteria: str,
    llm_output: str,
    image_count: int
):
    try:
        folder = os.path.join("llm_outputs", story_id)
        os.makedirs(folder, exist_ok=True)

        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        file_path = os.path.join(folder, f"{story_id}_{timestamp}.txt")

        # Try to pretty format JSON if possible
        try:
            parsed_json = json.loads(llm_output)
            formatted_output = json.dumps(parsed_json, indent=4)
        except Exception:
            formatted_output = llm_output  # fallback if not valid JSON

        with open(file_path, "w", encoding="utf-8") as f:
            f.write("=====================================\n")
            f.write("ADO INTELLIGENCE ANALYSIS OUTPUT\n")
            f.write("=====================================\n\n")

            f.write(f"Story ID: {story_id}\n")
            f.write(f"Title: {title}\n")
            f.write(f"Timestamp: {timestamp}\n")
            f.write(f"Image Count Sent To LLM: {image_count}\n\n")

            f.write("------------ DESCRIPTION ------------\n")
            f.write(description + "\n\n")

            f.write("------ ACCEPTANCE CRITERIA ----------\n")
            f.write(acceptance_criteria + "\n\n")

            f.write("---------- LLM STRUCTURED OUTPUT ----------\n")
            f.write(formatted_output + "\n")

        print(f"\n✅ LLM output saved to: {file_path}\n")

        return file_path

    except Exception as e:
        print(f"❌ Failed to write TXT output: {e}")
        return None
