from azure.search.documents import SearchClient
from azure.core.credentials import AzureKeyCredential
from azure.search.documents.models import VectorizedQuery

from openai import AzureOpenAI

from embeddingtovectordb.config import get
from channel_detector import detect_channels
from prompt_templates import build_testcase_prompt


class TestCaseRAGRetriever:

    def __init__(self):

        # -------- Azure AI Search --------
        self.search_client = SearchClient(
            endpoint=get("AZURE_SEARCH_ENDPOINT"),
            index_name=get("AZURE_SEARCH_INDEX"),
            credential=AzureKeyCredential(get("AZURE_SEARCH_KEY"))
        )

        # -------- Azure OpenAI --------
        self.openai = AzureOpenAI(
            api_key=get("AZURE_OPENAI_KEY"),
            azure_endpoint=get("AZURE_OPENAI_ENDPOINT"),
            api_version=get("AZURE_OPENAI_API_VERSION")
        )

        self.embed_model = get("EMBEDDING_MODEL")
        self.chat_model = get("CHAT_MODEL")
        self.top_k = get("TOP_K", int)

    # ----------------------------------------------------
    # Create embedding for query
    # ----------------------------------------------------
    def embed_query(self, text):
        print("🧠 Creating embedding from User Story + Description + AC...")

        emb = self.openai.embeddings.create(
            model=self.embed_model,
            input=text
        )

        vec = emb.data[0].embedding
        print(f"✅ Embedding length: {len(vec)}")
        return vec

    # ----------------------------------------------------
    # Step 1 — Vector retrieval from Azure AI Search
    # ----------------------------------------------------
    def retrieve(self, user_story, description, ac):

        print("\n🔹 Step 1: Detecting channels from AC")
        channels = detect_channels(ac)

        filter_query = " or ".join([f"channel eq '{c}'" for c in channels])
        print(f"🔎 Channel Filter: {filter_query}")

        print("\n🔹 Step 2: Preparing semantic query text")

        query_text = f"""
        User Story:
        {user_story}

        Description:
        {description}

        Acceptance Criteria:
        {ac}
        """

        query_vector = self.embed_query(query_text)

        print("\n🔹 Step 3: Sending vector search to Azure AI Search")

        vector_query = VectorizedQuery(
            kind="vector",
            vector=query_vector,
            k_nearest_neighbors=self.top_k,
            fields="embedding"
        )

        results = self.search_client.search(
            search_text=None,
            vector_queries=[vector_query],
            filter=filter_query,
            select=["testCaseId", "chunkId", "content", "channel"]
        )

        results_list = list(results)
        print(f"✅ Retrieved {len(results_list)} chunks from vector DB\n")

        return results_list

    # ----------------------------------------------------
    # Step 2 — Group chunks → rebuild testcases WITH channel
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

            chunks_sorted = sorted(data["chunks"], key=lambda x: int(x[0]))
            full_text = "\n".join([c[1] for c in chunks_sorted])

            final_context.append({
                "testCaseId": tcid,
                "channel": data["channel"],
                "full_text": full_text
            })

        print("✅ Historical context built\n")
        return final_context

    # ----------------------------------------------------
    # Step 3 — TRUE RAG: Send context to LLM
    # ----------------------------------------------------
    def generate_testcase_with_llm(
        self,
        user_story_id,
        user_story,
        description,
        ac,
        retrieved_chunks
    ):

        historical_testcases = self.build_context_by_testcase(retrieved_chunks)

        historical_context_text = ""
        channels = set()

        for tc in historical_testcases:
            channels.add(tc["channel"])
            historical_context_text += (
                f"\n\n### Historical TestCase: {tc['testCaseId']} "
                f"(Channel: {tc['channel']})\n"
                f"{tc['full_text']}\n"
            )

        prompt = build_testcase_prompt(
            user_story_id,
            user_story,
            description,
            ac,
            historical_context_text
        )

        print("🤖 Sending prompt to Azure OpenAI...\n")

        response = self.openai.chat.completions.create(
            model=self.chat_model,
            messages=[
                {"role": "system", "content": "You generate software test cases."},
                {"role": "user", "content": prompt}
            ],
            temperature=0.2
        )

        output = response.choices[0].message.content
        print("✅ LLM Response Received\n")

        return {
            "llm_text": output,
            "channels": list(channels)
        }
