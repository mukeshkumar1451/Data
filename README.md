Reading Excel files...
Processing: data/excels\Indiv_US_36164_Test Scripts_v1.0.xlsx
Processing: data/excels\Indiv_US_658373_Test Logs_v1.0.xlsx
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\pandas\core\indexes\base.py", line 3812, in get_loc
    return self._engine.get_loc(casted_key)
           ~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^
  File "pandas/_libs/index.pyx", line 167, in pandas._libs.index.IndexEngine.get_loc
  File "pandas/_libs/index.pyx", line 196, in pandas._libs.index.IndexEngine.get_loc
  File "pandas/_libs/hashtable_class_helper.pxi", line 7088, in pandas._libs.hashtable.PyObjectHashTable.get_item
  File "pandas/_libs/hashtable_class_helper.pxi", line 7096, in pandas._libs.hashtable.PyObjectHashTable.get_item
KeyError: 'Test Case ID / Test Script ID'

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\Embedding\main_ingest.py", line 11, in <module> 
    docs = excel_to_documents(file)
  File "C:\Users\h84609n\Desktop\Embedding\ingestion\excel_to_documents.py", line 26, in excel_to_documents
    df[COL_TC] = df[COL_TC].ffill()
                 ~~^^^^^^^^
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\pandas\core\frame.py", line 4113, in __getitem__
    indexer = self.columns.get_loc(key)
  File "C:\Users\h84609n\Desktop\Embedding\.venv\Lib\site-packages\pandas\core\indexes\base.py", line 3819, in get_loc
    raise KeyError(key) from err
KeyError: 'Test Case ID / Test Script ID'
(.venv) PS C:\Users\h84609n\Desktop\Embedding> 
