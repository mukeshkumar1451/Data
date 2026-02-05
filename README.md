(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> py main.py
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\main.py", line 7, in <module>
    ensure_index()
    ~~~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\embeddingtovectordb\index_manager.py", line 
34, in ensure_index
    SimpleField("id", SearchFieldDataType.String, key=True),
    ~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
TypeError: SimpleField() takes 0 positional arguments but 2 positional arguments (and 1 keyword-only argument) were given
