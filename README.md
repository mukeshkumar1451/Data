
'[SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1081)' thrown while requesting HEAD https://huggingface.co/BAAI/bge-reranker-base/resolve/main/config.json
Retrying in 1s [Retry 1/5].

❌ ERROR OCCURRED
Can't load the configuration of 'BAAI/bge-reranker-base'. If you were trying to load it from 'https://huggingface.co/models', make sure you don't have a local directory with the same name. Otherwise, make sure 'BAAI/bge-reranker-base' is the correct path to a directory containing a config.json file

📌 TRACEBACK:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\transformers\configuration_utils.py", line 624, in _get_config_dict
    resolved_config_file = cached_file(
        pretrained_model_name_or_path,
    ...<9 lines>...
        _commit_hash=commit_hash,
    )
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\transformers\utils\hub.py", line 276, in cached_file    file = cached_files(path_or_repo_id=path_or_repo_id, filenames=[filename], **kwargs)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\transformers\utils\hub.py", line 507, in cached_files
    raise e
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\transformers\utils\hub.py", line 419, in cached_files
    hf_hub_download(
    ~~~~~~~~~~~~~~~^
        path_or_repo_id,
        ^^^^^^^^^^^^^^^^
    ...<9 lines>...
        local_files_only=local_files_only,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\huggingface_hub\utils\_validators.py", line 89, in _inner_fn
    return fn(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\huggingface_hub\file_download.py", line 1024, in hf_hub_download
    return _hf_hub_download_to_cache_dir(
        # Destination
    ...<15 lines>...
        dry_run=dry_run,
    )
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\huggingface_hub\file_download.py", line 1157, in _hf_hub_download_to_cache_dir
    _get_metadata_or_catch_error(
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~^
        repo_id=repo_id,
        ^^^^^^^^^^^^^^^^
    ...<10 lines>...
        retry_on_errors=True,
        ^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\huggingface_hub\file_download.py", line 1691, in _get_metadata_or_catch_error
    metadata = get_hf_file_metadata(
        url=url,
    ...<4 lines>...
        retry_on_errors=retry_on_errors,
    )
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\huggingface_hub\utils\_validators.py", line 89, in _inner_fn
    return fn(*args, **kwargs)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\huggingface_hub\file_download.py", line 1614, in get_hf_file_metadata
    response = _httpx_follow_relative_redirects(
        method="HEAD", url=url, headers=hf_headers, timeout=timeout, retry_on_errors=retry_on_errors
    )
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\huggingface_hub\file_download.py", line 302, in _httpx_follow_relative_redirects
    response = http_backoff(
        method=method,
    ...<3 lines>...
        **no_retry_kwargs,
    )
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\huggingface_hub\utils\_http.py", line 506, in http_backoff
    return next(
        _http_backoff_base(
    ...<9 lines>...
        )
    )
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\huggingface_hub\utils\_http.py", line 414, in _http_backoff_base
    response = client.request(method=method, url=url, **kwargs)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\httpx\_client.py", line 825, in request
    return self.send(request, auth=auth, follow_redirects=follow_redirects)
           ~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\httpx\_client.py", line 901, in send
    raise RuntimeError("Cannot send a request, as the client has been closed.")
RuntimeError: Cannot send a request, as the client has been closed.

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 43, in <module>
    retriever = RAGRetriever()
  File "C:\Users\h84609n\Desktop\VectorDb Test\ragquery\rag_query.py", line 17, in __init__
    self.reranker = CrossEncoderReranker()
                    ~~~~~~~~~~~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\rerankerbase\reranker.py", line 6, in __init__
    self.model = CrossEncoder("BAAI/bge-reranker-base")
                 ~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\sentence_transformers\cross_encoder\util.py", line 39, in wrapper
    return func(self, *args, **kwargs)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\sentence_transformers\cross_encoder\CrossEncoder.py", line 155, in __init__
    config: PretrainedConfig = AutoConfig.from_pretrained(
                               ~~~~~~~~~~~~~~~~~~~~~~~~~~^
        model_name_or_path,
        ^^^^^^^^^^^^^^^^^^^
    ...<5 lines>...
        **config_kwargs,
        ^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\transformers\models\auto\configuration_auto.py", line 1376, in from_pretrained
    config_dict, unused_kwargs = PreTrainedConfig.get_config_dict(pretrained_model_name_or_path, **kwargs)
                                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\transformers\configuration_utils.py", line 569, in get_config_dict
    config_dict, kwargs = cls._get_config_dict(pretrained_model_name_or_path, **kwargs)
                          ~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\transformers\configuration_utils.py", line 646, in _get_config_dict
    raise OSError(
    ...<4 lines>...
    )
OSError: Can't load the configuration of 'BAAI/bge-reranker-base'. If you were trying to load it from 'https://huggingface.co/models', make sure you don't have a local directory with the same name. Otherwise, make sure 'BAAI/bge-reranker-base' is the correct path to a directory containing a config.json file
