
📄 Writing channel-specific testcases into Excel template...


❌ ERROR OCCURRED
'desc'

📌 TRACEBACK:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 110, in <module>
    exporter.export(
    ~~~~~~~~~~~~~~~^
        testcases=all_generated_testcases,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        user_story_id=user_story_id,
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 110, in <module>
    exporter.export(
    ~~~~~~~~~~~~~~~^
        testcases=all_generated_testcases,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        user_story_id=user_story_id,
    ~~~~~~~~~~~~~~~^
        testcases=all_generated_testcases,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        user_story_id=user_story_id,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        user_story_id=user_story_id,
        user_story_id=user_story_id,
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        output_path=output_file
        ^^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "C:\Users\h84609n\Desktop\VectorDb Test\excel_multi_sheet_exporter.py", line 49, in export
    ws.cell(row, 6).value = step["desc"]
                            ~~~~^^^^^^^^
KeyError: 'desc'
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test>
