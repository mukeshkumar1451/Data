(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> py test_rag.py       
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 14, in <module>
    story = load_userstory("userstory_input.yaml")
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 10, in load_userstory    
    return yaml.safe_load(f)
           ~~~~~~~~~~~~~~^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\__init__.py", line 125, in safe_load
    return load(stream, SafeLoader)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\__init__.py", line 79, in load
    loader = Loader(stream)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\loader.py", line 34, in __init__
    Reader.__init__(self, stream)
    ~~~~~~~~~~~~~~~^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\reader.py", line 85, in __init__
    self.determine_encoding()
    ~~~~~~~~~~~~~~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\reader.py", line 124, in determine_encoding
    self.update_raw()
    ~~~~~~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\reader.py", line 178, in update_raw
    data = self.stream.read(size)
  File "C:\Users\h84609n\AppData\Local\Programs\Python\Python314\Lib\encodings\cp1252.py", 
line 23, in decode
    return codecs.charmap_decode(input,self.errors,decoding_table)[0]
           ~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
UnicodeDecodeError: 'charmap' codec can't decode byte 0x9d in position 192: character maps 
to <undefined>
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> 
