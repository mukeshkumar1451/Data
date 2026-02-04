(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> py main.py
🛠️ Index 'testcase-vectordb-test1' not found — creating now...

retrievable is not a known attribute of class <class 'azure.search.documents.indexes.models._index.SearchField'> and will be ignored
✅ Index 'testcase-vectordb-test1' created successfully with chunkId support

🚀 Starting Excel → Vector DB upload...

 sheet='RTL' | test_case_id='718516_RTL_01' | steps=1
➡️ 718516_RTL_01: Single chunk
🧩 718516_RTL_01: Total chunks created = 1
   ⬆️ Uploading chunk 1/1
✅ 718516_RTL_01 upload completed

✅ Uploaded TestCase '718516_RTL_01' from sheet 'RTL'
 sheet='DTC' | test_case_id='718516_DTC_01' | steps=1
➡️ 718516_DTC_01: Single chunk
🧩 718516_DTC_01: Total chunks created = 1
   ⬆️ Uploading chunk 1/1
✅ 718516_DTC_01 upload completed

✅ Uploaded TestCase '718516_DTC_01' from sheet 'DTC'
 sheet='WHL' | test_case_id='718516_WHL_01' | steps=1
➡️ 718516_WHL_01: Single chunk
🧩 718516_WHL_01: Total chunks created = 1
   ⬆️ Uploading chunk 1/1
✅ 718516_WHL_01 upload completed

✅ Uploaded TestCase '718516_WHL_01' from sheet 'WHL'
 sheet='CL1' | test_case_id='718516_CL1_01' | steps=1
➡️ 718516_CL1_01: Single chunk
🧩 718516_CL1_01: Total chunks created = 1
   ⬆️ Uploading chunk 1/1
✅ 718516_CL1_01 upload completed

✅ Uploaded TestCase '718516_CL1_01' from sheet 'CL1'

🎉 All testcases uploaded successfully into Azure AI Search vector index
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> 
