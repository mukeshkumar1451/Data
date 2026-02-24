def run(self, state: Dict) -> Dict:

    logger.info("🤖 LLM Generator Running")

    # 🔥 clone full state safely
    new_state = dict(state)

    outputs = {}

    for channel, ctx in state["channel_context"].items():

        payload = {
            "user_story_id": state["user_story_id"],
            "user_story": state["user_story"],
            "description": state["description"],
            "ac": state["acceptance_criteria"],
            "channel": channel,
            "precondition": ctx["precondition"]
        }

        result = self.chain.invoke(payload)
        outputs[channel] = result.content.strip()

    new_state["llm_outputs"] = outputs

    # 🔥 DO NOT TOUCH selected_preconditions
    # Just preserve whatever came from retrieval

    return new_state
