❌ ERROR OCCURRED
'channel'

📌 TRACEBACK:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 83, in <module>
    exporter.export(parsed_steps, user_story_id, output_file)
    ~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\excel_multi_sheet_exporter.py", line 36, in export
    channel = tc["channel"]
              ~~^^^^^^^^^^^
KeyError: 'channel'
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> 
