import uuid
from openai import AzureOpenAI
from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from config import get

openai_client = AzureOpenAI(
    api_key=get("AZURE_OPENAI_KEY"),
    api_version=get("AZURE_OPENAI_API_VERSION"),
    azure_endpoint=get("AZURE_OPENAI_ENDPOINT")
)

search_client = SearchClient(
    endpoint=get("AZURE_SEARCH_ENDPOINT"),
    index_name=get("AZURE_SEARCH_INDEX"),
    credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
)

MAX_STEPS = get("MAX_STEPS_PER_CHUNK", int)


def build_chunks(group):
    chunks, current, count = [], "", 0

    for _, row in group.iterrows():
        step = str(row.get("Test Step No.", "")).strip()
        if not step.startswith("Step"):
            continue

        block = f"""{step}
{row.get('Test Step Description','')}
Screen: {row.get('Screen Name','')}
Data: {row.get('Test Data','')}
Expected: {row.get('Expected Results','')}

"""
        current += block
        count += 1

        if count == MAX_STEPS:
            chunks.append(current)
            current, count = "", 0

    if current:
        chunks.append(current)

    return chunks


def upload(sheet, tc, group, step_count):
    chunks = build_chunks(group)

    print(f"➡️ {tc}: {'Single' if step_count<=MAX_STEPS else 'Multi'} chunk ({len(chunks)})")

    for idx, text in enumerate(chunks, 1):
        content = f"TestCase: {tc}\nChannel: {sheet}\nChunk:{idx}\n\n{text}"

        emb = openai_client.embeddings.create(
            model=get("EMBEDDING_MODEL"),
            input=content
        ).data[0].embedding

        doc = {
            "id": str(uuid.uuid4()),
            "testCaseId": tc,
            "chunkId": idx,
            "channel": sheet,
            "content": content,
            "embedding": emb
        }

        search_client.upload_documents([doc])

    print(f"✅ {tc} uploaded\n")
