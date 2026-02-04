PS C:\Users\h84609n\Desktop\Access> C:/Users/h84609n/Desktop/Access/.venv/Scripts/python.exe azure_access_test.py

🔹 Testing Azure OpenAI...
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\Access\azure_access_test.py", line 100, in <module>
    test_openai()
    ~~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\Access\azure_access_test.py", line 15, in test_openai
    emb = client.embeddings.create(
        model=OPENAI_EMBED_MODEL,
        input="Azure access test"
    )
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\openai\resources\embeddings.py", line 132, in create
    return self._post(
           ~~~~~~~~~~^
        "/embeddings",
        ^^^^^^^^^^^^^^
    ...<8 lines>...
        cast_to=CreateEmbeddingResponse,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\openai\_base_client.py", line 1294, in post
    return cast(ResponseT, self.request(cast_to, opts, stream=stream, stream_cls=stream_cls))
                           ~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\openai\_base_client.py", line 1067, in request
    raise self._make_status_error_from_response(err.response) from None
openai.NotFoundError: Error code: 404 - {'error': {'code': 'DeploymentNotFound', 'message': 'The API deployment for this resource 
does not exist. If you created the deployment within the last 5 minutes, please wait a moment and try again.'}}
PS C:\Users\h84609n\Desktop\Access> 
