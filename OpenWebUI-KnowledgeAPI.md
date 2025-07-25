# Files / Knowledge API for OpenWebUI

Open-WebUI offers a [partial API](https://docs.openwebui.com/getting-started/api-endpoints) to upload files and add them 
to knowledge bases. It allows uploading files and 

## Uploading Files
To utilize external data in RAG responses, you first need to upload the files. The content of the uploaded file is automatically extracted and stored in a vector database. With the filename attribute we can encode the full file path, separators are encoded as `%%`.

Endpoint: POST /api/v1/files/

Curl Example:

```shell
curl -X POST -H "Authorization: Bearer YOUR_API_KEY" -H "Accept: application/json" \
-F "file=@path/to/your/file;filename=path%%to%%your%%file" http://localhost:3000/api/v1/files/
```

Sample run:
```
curl -X POST -H "Authorization: Bearer sk-1084097265804661907951d6be1ac8d1" -H "Accept: application/json" -F "file=@x/knowledgetest.txt" http://localhost:3000/api/v1/files/
{"id":"2ec72510-cfc3-4d5c-babf-a30bd4d9d976","user_id":"426b5aea-7079-499f-b139-5471c33aeecd","hash":"36bb78f0cb9466315a1bdd13e7cde298d4ce33faac6c175b39bb9f1414f38799","filename":"knowledgetest.txt","data":{"content":"Ein Hubbu ist ein grosses Auto.\n"},"meta":{"name":"knowledgetest.txt","content_type":"text/plain","size":32,"data":{},"collection_name":"file-2ec72510-cfc3-4d5c-babf-a30bd4d9d976"},"created_at":1753170693,"updated_at":1753170693}~/Downloads>
```

Python Example:

```python
import requests

def upload_file(token, file_path):
url = 'http://localhost:3000/api/v1/files/'
headers = {
'Authorization': f'Bearer {token}',
'Accept': 'application/json'
}
files = {'file': open(file_path, 'rb')}
response = requests.post(url, headers=headers, files=files)
return response.json()
```

## Getting a list of uploaded files

GET /api/v1/files/

Yields a list of metadata (see metadata result returned by upload above) for all uploaded files.

## Adding Files to Knowledge Collections

After uploading, you can group files into a knowledge collection or reference them individually in chats.

Endpoint: POST /api/v1/knowledge/{id}/file/add

Curl Example:

```shell
curl -X POST http://localhost:3000/api/v1/knowledge/{knowledge_id}/file/add \
-H "Authorization: Bearer YOUR_API_KEY" \
-H "Content-Type: application/json" \
-d '{"file_id": "your-file-id-here"}'
```

Python Example:

```python
import requests

def add_file_to_knowledge(token, knowledge_id, file_id):
url = f'http://localhost:3000/api/v1/knowledge/{knowledge_id}/file/add'
headers = {
'Authorization': f'Bearer {token}',
'Content-Type': 'application/json'
}
data = {'file_id': file_id}
response = requests.post(url, headers=headers, json=data)
return response.json() 
```

## Delete a file from file storage

When deleting it from the knowledge collection this seems unneccessary - the file is deleted from the storage automatically.

    curl -X DELETE -H "Authorization: Bearer XXX" -H "Accept: application/json" http://localhost:3000/api/v1/files/{fileid} | jq


## List Knowledge Collections

    curl -X GET -H "Authorization: Bearer XXX" -H "Accept: application/json" http://localhost:3000/api/v1/knowledge/ | jq

Just use to find out IDs.

## List Knowledge Files in one Collection

    curl -X GET -H "Authorization: Bearer XXX" -H "Accept: application/json" http://localhost:3000/api/v1/knowledge/{id} | jq

Result (some parts omitted for brevity):
```json
{
  "id": "a836f4b9-2ce6-4d30-bd28-ebd56c59eab3",
  "name": "testknowledge",
  "description": "test working with knowledge",
  "data": {
    "file_ids": [
      "b7d58668-8f5f-488b-ac0a-b57fa62f7aad"
    ]
  },
  "created_at": 1753127761,
  "updated_at": 1753214984,
  "files": [
    {
      "id": "b7d58668-8f5f-488b-ac0a-b57fa62f7aad",
      "meta": {
        "name": "knowledgetest.txt",
        "content_type": "text/plain",
        "size": 32,
        "data": {},
        "collection_name": "a836f4b9-2ce6-4d30-bd28-ebd56c59eab3"
      },
      "created_at": 1753162588,
      "updated_at": 1753162588
    }
  ]
}
```

## Removing Files from Knowledge Collections

To remove a file from a knowledge collection:

Endpoint: POST /api/v1/knowledge/{knowledge_id}/file/remove

Curl Example:

```shell
curl -X POST http://localhost:3000/api/v1/knowledge/{knowledge_id}/file/remove \
-H "Authorization: Bearer YOUR_API_KEY" \
-H "Content-Type: application/json" \
-d '{"file_id": "your-file-id-here"}'
```
