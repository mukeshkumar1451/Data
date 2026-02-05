❌ ERROR OCCURRED
expected str, bytes or os.PathLike object, not NoneType

📌 TRACEBACK:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 83, in <module>
    exporter.export(parsed_steps, user_story_id, output_file)
    ~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\excel_multi_sheet_exporter.py", line 24, in export
    wb = load_workbook(self.template_path)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\openpyxl\reader\excel.py", line 346, in load_workbook
    reader = ExcelReader(filename, read_only, keep_vba,
                         data_only, keep_links, rich_text)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\openpyxl\reader\excel.py", line 123, in __init__
    self.archive = _validate_archive(fn)
                   ~~~~~~~~~~~~~~~~~^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\openpyxl\reader\excel.py", line 77, in _validate_archive
    file_format = os.path.splitext(filename)[-1].lower()
                  ~~~~~~~~~~~~~~~~^^^^^^^^^^
  File "<frozen ntpath>", line 244, in splitext
TypeError: expected str, bytes or os.PathLike object, not NoneType
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> 
