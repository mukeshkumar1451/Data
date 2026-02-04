(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> py vectordbcheck.py
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\vectordbcheck.py", line 36, in <module>
    xls = pd.ExcelFile(EXCEL_PATH)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\pandas\io\excel\_base.py", line 1604, in __init__
    ext = inspect_excel_format(
        content_or_path=path_or_buffer, storage_options=storage_options
    )
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\pandas\io\excel\_base.py", line 1452, in inspect_excel_format
    with get_handle(
         ~~~~~~~~~~^
        content_or_path, "rb", storage_options=storage_options, is_text=False    
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^    
    ) as handle:
    ^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\pandas\io\common.py", line 935, in get_handle
    handle = open(handle, ioargs.mode)
FileNotFoundError: [Errno 2] No such file or directory: 'excel\\Indiv_US_718516_Test Scripts_v1.0.xlsx;excel'
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> py vectordbcheck.py

📄 Processing sheet: RTL
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}

📄 Processing sheet: DTC
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}

📄 Processing sheet: WHL
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}

📄 Processing sheet: CL1
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}
❌ Error: Error code: 404 - {'error': {'code': '404', 'message': 'Resource not fou
nd'}}

🎉 All sheets uploaded to Azure AI Search vector DB
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> 
