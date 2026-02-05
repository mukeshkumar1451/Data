❌ ERROR OCCURRED
while scanning for the next token
found character '\t' that cannot start any token
  in "userstory_input.yaml", line 9, column 12  

📌 TRACEBACK:

Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 27, in <module>
    story = load_userstory("userstory_input.yaml")
  File "C:\Users\h84609n\Desktop\VectorDb Test\test_rag.py", line 15, in load_userstory
    data = yaml.safe_load(f)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\__init__.py", line 125, in safe_load
    return load(stream, SafeLoader)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\__init__.py", line 81, in load
    return loader.get_single_data()
           ~~~~~~~~~~~~~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\constructor.py", line 49, in get_single_data
    node = self.get_single_node()
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\composer.py", line 36, in get_single_node
    document = self.compose_document()
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\composer.py", line 55, in compose_document
    node = self.compose_node(None, None)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\composer.py", line 84, in compose_node
    node = self.compose_mapping_node(anchor)
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\composer.py", line 127, in compose_mapping_node
    while not self.check_event(MappingEndEvent):
              ~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\parser.py", line 98, in check_event
    self.current_event = self.state()
                         ~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\parser.py", line 428, in parse_block_mapping_key
    if self.check_token(KeyToken):
       ~~~~~~~~~~~~~~~~^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\scanner.py", line 116, in check_token
    self.fetch_more_tokens()
    ~~~~~~~~~~~~~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\VectorDb Test\.venv\Lib\site-packages\yaml\scanner.py", line 258, in fetch_more_tokens
    raise ScannerError("while scanning for the next token", None,
            "found character %r that cannot start any token" % ch,
            self.get_mark())
yaml.scanner.ScannerError: while scanning for the next token
found character '\t' that cannot start any token
  in "userstory_input.yaml", line 9, column 12
(.venv) PS C:\Users\h84609n\Desktop\VectorDb Test> 
