# Requirement for knowledge synchronization

- The tool can be started in a directory containing the knowledge files. The directory is read recursively.
- A configuration file .open-webui-knowledgesync is required in the directory - if that is not present, the tool will
  not start.
- Format of the configuration file:

```shell
knowledge_base_id=<knowledge_base_id>
open-webui-api-key=<api_key>
open_webui_api_url=http://localhost:3000
kbdir_id=<identifiert for directory>
```

- The script will read the current content of the knowledge base and compare it with the files in the directory. If
  files are not there anymore or have been changed, they will be deleted. Whether the file content is checked with
  sha256
- The implementation is plain node.js without additional dependencies. No external libraries have to be installed.
- On uploading the files the file paths relative to this directory are encoded with %% as separator instead of /. The
  kbdir_id is put in front of the file path to support multiple directories. E.g. foo/bar/baz.txt will be encoded as
  kbdir_id%%foo%%bar%%baz.txt where kbdir_id is the identifier for the directory from the configuration file.
- Files starting with . are ignored.