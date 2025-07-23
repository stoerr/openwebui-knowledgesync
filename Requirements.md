# Requirement for knowledge synchronization

- The tool can be started in a directory containing the knowledge files.
- A configuration file .open-webui-knowledgesync is required in the directory - if that is not present, the tool will not start.
- Format of the configuration file:
```shell
knowledge_base_id=<knowledge_base_id>
open-webui-api-key=<api_key>
open_webui_api_url=http://localhost:3000
```
- The script will read the current content of the knowledge base and compare it with the files in the directory. If files are not there anymore or have been changed, they will be deleted. Whether the file content is checked with sha256