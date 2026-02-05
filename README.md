
📄 Writing test cases into Excel template...


❌ ERROR OCCURRED
MultiSheetExcelExporter.export() got an unexpected keyword argument 'channels'

📌 TRACEBACK:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 94, in <module>
    exporter.export(
    ~~~~~~~~~~~~~~~^
        testcases=parsed_steps,
        ^^^^^^^^^^^^^^^^^^^^^^^
    ...<2 lines>...
        channels=channels
        ^^^^^^^^^^^^^^^^^
    )
    ^
TypeError: MultiSheetExcelExporter.export() got an unexpected keyword argument 'channels'
