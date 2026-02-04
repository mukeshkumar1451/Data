# vector_uploader.py
import uuid

STEPS_PER_CHUNK = 8


def build_chunks(group):
    chunks = []
    current = ""
    counter = 0

    for _, row in group.iterrows():
        step_no = str(row.get("Test Step No.", "")).strip()

        if not step_no.startswith("Step"):
            continue

        step_text = f"""
{step_no}
{row.get('Test Step Description', '')}
Screen: {row.get('Screen Name', '')}
Data: {row.get('Test Data', '')}
Expected: {row.get('Expected Results', '')}
"""
        current += step_text
        counter += 1

        if counter == STEPS_PER_CHUNK:
            chunks.append(current)
            current = ""
            counter = 0

    if current:
        chunks.append(current)

    return chunks


def upload_testcase(openai_client, search_client, embed_deployment,
                    channel, test_case_id, group, step_count):

    first = group.iloc[0]

    # ---- Decide chunking ----
    if step_count <= STEPS_PER_CHUNK:
        print(f"➡️ {test_case_id}: Single chunk")
    else:
        print(f"➡️ {test_case_id}: Splitting into chunks of {STEPS_PER_CHUNK} steps")

    step_chunks = build_chunks(group)

    print(f"🧩 {test_case_id}: Total chunks created = {len(step_chunks)}")

    # ---- Upload each chunk ----
    for idx, steps_block in enumerate(step_chunks, start=1):
        print(f"   ⬆️ Uploading chunk {idx}/{len(step_chunks)}")

        content = f"""
Test Case: {test_case_id}
Channel: {channel}
Chunk: {idx}

Steps:
{steps_block}
"""

        emb = openai_client.embeddings.create(
            model=embed_deployment,
            input=content
        ).data[0].embedding

        document = {
            "id": str(uuid.uuid4()),
            "testCaseId": str(test_case_id),
            "chunkId": idx,
            "channel": channel,
            "content": content,
            "embedding": emb
        }

        search_client.upload_documents([document])

    print(f"✅ {test_case_id} upload completed\n")
