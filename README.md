# vector_uploader.py
import uuid

def build_content(channel, test_case_id, group):
    first = group.iloc[0]

    def g(col): return first.get(col, "")

    steps_text = ""
    for _, row in group.iterrows():
        steps_text += f"""
Step {row.get('Test Step No.', '')}:
{row.get('Test Step Description', '')}
Screen: {row.get('Screen Name', '')}
Test Data: {row.get('Test Data', '')}
Expected: {row.get('Expected Results', '')}
"""

    content = f"""
Test Case: {test_case_id}
Channel: {channel}

Scenario:
{g('Test Scenario Description')}

Script:
{g('Test Script Description')}

Precondition:
{g('Pre-Condition & Assumptions')}

All Steps:
{steps_text}
"""
    return content, g("Requirement Mapping")


def upload_testcase(openai_client, search_client, embed_deployment, channel, test_case_id, group):
    content, requirement = build_content(channel, test_case_id, group)

    emb = openai_client.embeddings.create(
        model=embed_deployment,
        input=content
    ).data[0].embedding

    document = {
        "id": str(uuid.uuid4()),
        "testCaseId": str(test_case_id),
        "requirementMapping": str(requirement),
        "channel": channel,
        "content": content,
        "embedding": emb
    }

    search_client.upload_documents([document])
