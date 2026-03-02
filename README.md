.venv) PS C:\Users\h84609n\Desktop\sharepointconnection> py search.py
✅ Token acquired

✅ Site: National Operations
Traceback (most recent call last):
  File "C:\Users\h84609n\Desktop\sharepointconnection\search.py", line 111, in <module>
    search_and_read(token, site_id, FILE_NAME)
    ~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\h84609n\Desktop\sharepointconnection\search.py", line 71, in search_and_read
    res.raise_for_status()
    ~~~~~~~~~~~~~~~~~~~~^^
  File "C:\Users\h84609n\Desktop\sharepointconnection\.venv\Lib\site-packages\requests\models.py", line 1026, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://graph.microsoft.com/v1.0/sites/corpofficeapps.sharepoint.com,bf088619-af20-4ee0-aed9-f59eadef4cc4,985d34fb-093d-449c-a958-0865ce7aac7d/drive/root/search(q='sKnowledge%20Modelling%20Approach%20Document.docx')
(.venv) PS C:\Users\h84609n\Desktop\sharepointconnection> 
