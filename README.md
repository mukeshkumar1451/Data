import os
from openai import AzureOpenAI

endpoint = "https://centralus.api.cognitive.microsoft.com/"
model_name = "text-embedding-3-large"
deployment = "text-embedding-3-large"

api_version = "2024-02-01"

client = AzureOpenAI(
    api_version="2024-12-01-preview",
    endpoint=endpoint,
    credential=AzureKeyCredential("<API_KEY>")
)

response = client.embeddings.create(
    input=["first phrase","second phrase","third phrase"],
    model=deployment
)

for item in response.data:
    length = len(item.embedding)
    print(
        f"data[{item.index}]: length={length}, "
        f"[{item.embedding[0]}, {item.embedding[1]}, "
        f"..., {item.embedding[length-2]}, {item.embedding[length-1]}]"
    )
print(response.usage)

