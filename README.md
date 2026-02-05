# ----------------------------------------------------
# Group chunks → rebuild testcases WITH channel
# ----------------------------------------------------
def build_context_by_testcase(self, results):

    print("🧩 Rebuilding historical testcases from chunks...")

    tc_map = {}

    for r in results:
        tcid = r["testCaseId"]
        channel = r["channel"]

        if tcid not in tc_map:
            tc_map[tcid] = {
                "channel": channel,
                "chunks": []
            }

        tc_map[tcid]["chunks"].append((r["chunkId"], r["content"]))

    final_context = []

    for tcid, data in tc_map.items():
        print(f"   ↳ Rebuilding TestCase: {tcid}")
        chunks_sorted = sorted(data["chunks"], key=lambda x: x[0])
        full_text = "\n".join([c[1] for c in chunks_sorted])

        final_context.append({
            "testCaseId": tcid,
            "channel": data["channel"],
            "full_text": full_text
        })

    return final_context
