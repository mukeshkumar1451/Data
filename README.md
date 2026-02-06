# ----------------------------------------------------
# Split reranked chunks by channel
# ----------------------------------------------------
def group_by_channel(self, reranked_chunks):
    channel_map = {}

    for r in reranked_chunks:
        ch = r["channel"]
        channel_map.setdefault(ch, []).append(r)

    return channel_map

    ---------------------------------------
def generate_testcase_with_llm(
    self,
    user_story_id,
    user_story,
    description,
    ac,
    retrieved_chunks
):

    channel_groups = self.group_by_channel(retrieved_chunks)

    final_outputs = {}

    for channel, chunks in channel_groups.items():

        print(f"\n🧠 Generating testcase using ONLY {channel} history")

        historical_context = self._build_historical_context(chunks)

        prompt = build_testcase_prompt(
            user_story_id,
            user_story,
            description,
            ac,
            historical_context
        )

        response = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[
                {"role": "system", "content": "You generate software test cases."},
                {"role": "user", "content": prompt}
            ],
            temperature=0.2
        )

        llm_text = response.choices[0].message.content

        final_outputs[channel] = llm_text

    return final_outputs
    
    
