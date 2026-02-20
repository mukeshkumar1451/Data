Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\sharepointconnection\graphapi.py", line 70, in <module>
    content = read_sharepoint_doc(token)
  File "C:\Users\h84609n\Desktop\sharepointconnection\graphapi.py", line 42, in read_sharepoint_doc
    res.raise_for_status()
    ~~~~~~~~~~~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\sharepointconnection\.venv\Lib\site-packages\requests\models.py", line 1026, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 400 Client Error: Bad Request for url: https://graph.microsoft.com/v1.0/sites/corpofficeapps.sharepoint.com:/sites/Ops_Home:/drive/root:/nationalops/Strategic%20Initiatives%20Team%20Folder/Cognizant%20UAT%20Results/Documents.doc:/content
