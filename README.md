ss/.venv/Scripts/python.exe azure_access_test.py

🔹 Testing Azure OpenAI...
✅ Embedding length: 3072
✅ Chat response: Yes, Azure OpenAI Service is operational and wor
king. It allows businesses and developers to access OpenAI's powerful models, such as GPT, Codex, and DALL·E, via the Azure platform. By integrating OpenAI models with Azure's cloud infrastructure, businesses can scale their AI workloads efficiently while maintaining security, compliance, and enterprise-grade features. You 
can use Azure OpenAI for tasks like content generation, code assistance, natural language understanding, and more. If you’re experiencing any issues or need insights, feel free to ask!

🔹 Testing Azure AI Search...
✅ Index created successfully

🔹 Testing Cosmos DB...
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\database.py", line 645, in create_container_if_not_exists
    properties = container_proxy.read(
        populate_query_metrics=populate_query_metrics,
        initial_headers=initial_headers,
        **kwargs
    )
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\core\tracing\decorator.py", line 119, in wrapper_use_tracer 
    return func(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\container.py", line 207, in read
    container = self.client_connection.ReadContainer(self.container_link, options=request_options, **kwargs)
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_cosmos_client_connection.py", line 626, in ReadContainer
    return self.Read(path, http_constants.ResourceType.Collection, collection_id, None, options, **kwargs)
           ~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_cosmos_client_connection.py", line 2911, in Read    
    result, last_response_headers = self.__Get(path, request_params, headers, **kwargs)
                                    ~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_cosmos_client_connection.py", line 2981, in __Get   
    return synchronized_request.SynchronizedRequest(
           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^
        client=self,
        ^^^^^^^^^^^^
    ...<6 lines>...
        **kwargs
        ^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_synchronized_request.py", line 236, in SynchronizedRequest
    return _retry_utility.Execute(
           ~~~~~~~~~~~~~~~~~~~~~~^
        client,
        ^^^^^^^
    ...<6 lines>...
        **kwargs
        ^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_retry_utility.py", line 129, in Execute
    result = ExecuteFunction(function, global_endpoint_manager, *args, **kwargs)
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_retry_utility.py", line 279, in ExecuteFunction     
    return function(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_synchronized_request.py", line 170, in _Request     
    raise exceptions.CosmosResourceNotFoundError(message=data, response=response)
azure.cosmos.exceptions.CosmosResourceNotFoundError: (NotFound) Message: {"Errors":["Resource Not Found. Learn more: https://aka.ms/cosmosdb-tsg-not-found"]}
ActivityId: 4afd8280-f19b-4d41-ad1f-b5c7133826f2, Request URI: /apps/aef7118e-7162-42c3-959e-bafc8a85f620/services/ab2de13f-a07c-44e6-9400-b16d74551161/partitions/f38462d1-1518-42eb-aaa0-4300b28e30c3/replicas/134142153072955857s, RequestStats: , SDK: Microsoft.Azure.Documents.Common/2.14.0
Code: NotFound
Message: Message: {"Errors":["Resource Not Found. Learn more: https://aka.ms/cosmosdb-tsg-not-found"]}
ActivityId: 4afd8280-f19b-4d41-ad1f-b5c7133826f2, Request URI: /apps/aef7118e-7162-42c3-959e-bafc8a85f620/services/ab2de13f-a07c-44e6-9400-b16d74551161/partitions/f38462d1-1518-42eb-aaa0-4300b28e30c3/replicas/134142153072955857s, RequestStats: , SDK: Microsoft.Azure.Documents.Common/2.14.0

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\Access\azure_access_test.py", line 102, in <module>
    test_cosmos()
    ~~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\Access\azure_access_test.py", line 83, in test_cosmos
    container = db.create_container_if_not_exists(
        id=CONTAINER_NAME,
        partition_key=PartitionKey(path="/userStoryId")
    )
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\core\tracing\decorator.py", line 119, in wrapper_use_tracer 
    return func(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\database.py", line 654, in create_container_if_not_exists
    return self.create_container(
           ~~~~~~~~~~~~~~~~~~~~~^
        id=id,
        ^^^^^^
    ...<14 lines>...
        **kwargs
        ^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\core\tracing\decorator.py", line 119, in wrapper_use_tracer 
    return func(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\database.py", line 435, in create_container
    result = self.client_connection.CreateContainer(
        database_link=self.database_link, collection=definition, 
options=request_options, **kwargs
    )
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_cosmos_client_connection.py", line 568, in CreateContainer
    return self.Create(collection, path, http_constants.ResourceType.Collection, database_id, None,
           ~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                       options, **kwargs)
                       ^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_cosmos_client_connection.py", line 2764, in Create  
    result, last_response_headers = self.__Post(path, request_params, body, headers, **kwargs)
                                    ~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_cosmos_client_connection.py", line 3010, in __Post  
    return synchronized_request.SynchronizedRequest(
           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^
        client=self,
        ^^^^^^^^^^^^
    ...<6 lines>...
        **kwargs
        ^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_synchronized_request.py", line 236, in SynchronizedRequest
    return _retry_utility.Execute(
           ~~~~~~~~~~~~~~~~~~~~~~^
        client,
        ^^^^^^^
    ...<6 lines>...
        **kwargs
        ^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_retry_utility.py", line 129, in Execute
    result = ExecuteFunction(function, global_endpoint_manager, *args, **kwargs)
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_retry_utility.py", line 279, in ExecuteFunction     
    return function(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\Access\.venv\Lib\site-packages\azure\cosmos\_synchronized_request.py", line 176, in _Request     
    raise exceptions.CosmosHttpResponseError(message=data, response=response)
azure.cosmos.exceptions.CosmosHttpResponseError: (BadRequest) Partition key path /userStoryId is invalid for MongoDB API.
ActivityId: 9dbec594-77e0-4a6b-a3be-a0752716b5ad, Microsoft.Azure.Documents.Common/2.14.0
Code: BadRequest
Message: Partition key path /userStoryId is invalid for MongoDB API.
ActivityId: 9dbec594-77e0-4a6b-a3be-a0752716b5ad, Microsoft.Azure.Documents.Common/2.14.0
PS C:\Users\h84609n\Desktop\Access>

