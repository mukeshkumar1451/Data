# vector_uploader.py
import uuid

STEPS_PER_CHUNK = 8


def build_single_chunk(group):
    text = ""
    for _, row in group.iterrows():
        text += f"""
Step {row.get('Test Step No.', '')}:
{row.get('Test Step Description', '')}
Screen: {row.get('Screen Name', '')}
Test Data: {row.get('Test Data', '')}
Expected: {row.get('Expected Results', '')}
"""
    return [text]   # return as list (1 chunk)


def build_multi_chunks(group):
    chunks = []
    current = ""
    counter = 0

    for _, row in group.iterrows():
        step_text = f"""
Step {row.get('Test Step No.', '')}:
{row.get('Test Step Description', '')}
Screen: {row.get('Screen Name', '')}
Test Data: {row.get('Test Data', '')}
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
                    channel, test_case_id, group):

    first = group.iloc[0]
    step_count = len(group)

    # ✅ DECISION happens here
    if step_count <= STEPS_PER_CHUNK:
        step_chunks = build_single_chunk(group)
        print(f"➡️ {test_case_id}: Single chunk ({step_count} steps)")
    else:
        step_chunks = build_multi_chunks(group)
        print(f"➡️ {test_case_id}: Split into {len(step_chunks)} chunks ({step_count} steps)")

    # ---- Upload each chunk ----
    for idx, steps_block in enumerate(step_chunks, start=1):

        content = f"""
Test Case: {test_case_id}
Channel: {channel}
Chunk: {idx}

Scenario:
{first.get('Test Scenario Description', '')}

Script:
{first.get('Test Script Description', '')}

Precondition:
{first.get('Pre-Condition & Assumptions', '')}

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
            "requirementMapping": str(first.get("Requirement Mapping", "")),
            "channel": channel,
            "content": content,
            "embedding": emb
        }

        search_client.upload_documents([document])
