state["story_id"] = story_id
state["title"] = story.get("title")
state["user_story"] = story.get("title")
state["description"] = desc_data["clean_text"]

# 🔥 THIS IS IMPORTANT
state["acceptance_criteria"] = grouped_steps

state["llm_output"] = llm_output
