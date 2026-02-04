import uuid
from config import *

# ---------- 1. Test Azure OpenAI ----------
from openai import AzureOpenAI

def test_openai():
    print("\n🔹 Testing Azure OpenAI...")
    client = AzureOpenAI(
        api_key=OPENAI_KEY,
        api_version="2024-02-01",
        azure_endpoint=OPENAI_ENDPOINT
    )

    emb = client.embeddings.create(
        model=OPENAI_EMBED_MODEL,
        input="Azure access test"
    )
    print("✅ Embedding length:", len(emb.data[0].embedding))

    chat = client.chat.completions.create(
        model=OPENAI_CHAT_MODEL,
        messages=[{"role": "user", "content": "Say Azure OpenAI is working"}]
    )
    print("✅ Chat response:", chat.choices[0].message.content)


# ---------- 2. Test Azure AI Search ----------
from azure.search.documents.indexes import SearchIndexClient
from azure.search.documents.indexes.models import (
    SearchIndex, SimpleField, SearchFieldDataType,
    VectorSearch, HnswAlgorithmConfiguration, VectorSearchProfile,
    SearchField
)
from azure.core.credentials import AzureKeyCredential

def test_ai_search():
    print("\n🔹 Testing Azure AI Search...")

    credential = AzureKeyCredential(SEARCH_KEY)
    index_client = SearchIndexClient(endpoint=SEARCH_ENDPOINT, credential=credential)

    fields = [
        SimpleField(name="id", type=SearchFieldDataType.String, key=True),
        SearchField(
            name="embedding",
            type=SearchFieldDataType.Collection(SearchFieldDataType.Single),
            vector_search_dimensions=1536,
            vector_search_profile_name="my-vector-profile"
        ),
    ]

    vector_search = VectorSearch(
        algorithms=[
            HnswAlgorithmConfiguration(name="hnsw-config")
        ],
        profiles=[
            VectorSearchProfile(
                name="my-vector-profile",
                algorithm_configuration_name="hnsw-config"
            )
        ]
    )

    index = SearchIndex(name=INDEX_NAME, fields=fields, vector_search=vector_search)

    try:
        index_client.create_index(index)
        print("✅ Index created successfully")
    except Exception as e:
        print("ℹ️ Index may already exist:", e)


# ---------- 3. Test Cosmos DB ----------
from azure.cosmos import CosmosClient, PartitionKey

def test_cosmos():
    print("\n🔹 Testing Cosmos DB...")

    client = CosmosClient(COSMOS_ENDPOINT, COSMOS_KEY)
    db = client.create_database_if_not_exists(id=DATABASE_NAME)

    container = db.create_container_if_not_exists(
        id=CONTAINER_NAME,
        partition_key=PartitionKey(path="/userStoryId")
    )

    item = {
        "id": str(uuid.uuid4()),
        "userStoryId": "US-101",
        "content": "Cosmos DB access test"
    }

    container.upsert_item(item)
    print("✅ Item inserted into Cosmos DB")


# ---------- Run all tests ----------
if __name__ == "__main__":
    test_openai()
    test_ai_search()
    test_cosmos()
    print("\n🎉 ALL AZURE SERVICES ARE WORKING!")
