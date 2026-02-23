def _build_channel_context(self, full_story: str, channel: str):

    tests = self._vector_retrieve(full_story, channel, 40)

    # 🔥 sanitize BEFORE rerank
    sanitized_initial = self._sanitize_docs(channel, tests)

    if not sanitized_initial:
        logger.warning(f"⚠️ No docs left after sanitization for {channel}")
        sanitized_initial = tests[:5]  # safe fallback

    reranked = self._rerank_testcases(full_story, sanitized_initial)

    if not reranked:
        logger.warning(f"⚠️ No reranked docs for {channel}, using sanitized raw")
        reranked = sanitized_initial[:5]

    setup = self._infer_setup(channel, full_story, reranked)

    return {
        "tests": reranked,
        "setup": setup
    }
