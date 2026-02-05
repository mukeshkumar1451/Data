📄 Writing test cases into Excel template...


❌ ERROR OCCURRED
'scenario'

📌 TRACEBACK:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 94, in <module>
    exporter.export(
    ~~~~~~~~~~~~~~~^
        testcases=parsed_steps,
        ^^^^^^^^^^^^^^^^^^^^^^^
    ...<2 lines>...


    )
    ^
  File "C:\Users\h84609n\Desktop\VectorDb Test\excel_multi_sheet_exporter.py", line 56, in export
    scenario = tc["scenario"]
               ~~^^^^^^^^^^^^
KeyError: 'scenario'
