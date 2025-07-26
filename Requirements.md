# Requirement for knowledge synchronization

- The tool can be started in a directory containing the knowledge files. The directory is read recursively.
- A configuration file .open-webui-knowledgesync is required in the directory - if that is not present, the tool will
  not start.
- The format of the configuration file is:

```shell
knowledge_base_id=<knowledge_base_id>
open-webui-api-key=<api_key>
open_webui_api_url=http://localhost:3000
kbdir_id=<identifiert for directory>
fileregex=<regex for files to be included>
```

- The script will read the current content of the knowledge base and compare it with the files in the directory. If
  files are not there anymore or have been changed, they will be deleted. Whether the file content is checked with
  sha256
- The implementation is plain node.js without additional dependencies. No external libraries have to be installed.
- On uploading the files the file paths relative to this directory are encoded with %% as separator instead of a slash.
  The
  kbdir_id is put in front of the file path to support multiple directories. E.g. foo/bar/baz.txt will be encoded as
  kbdir_id%%foo%%bar%%baz.txt where kbdir_id is the identifier for the directory from the configuration file.
- When uploading a file the result is checked for success: the file id has to be present in the response. Similarily:
  when adding the file to a knowledge collection an id has to be present in the response. If not, the file must be
  deleted again to save space.
- When deleting a file it must be first deleted from the knowledge collection .
- At sync start, a list of all files is fetched from OpenWebUI. Before uploading a file we check whether a file with the
  same name and sha256 hash as the file to be uploaded already exists. If so, the file is not uploaded again, but the
  file id is used to add the file to the knowledge collection.
- Files starting with . are ignored.
- Only files matching the fileregex are included.
