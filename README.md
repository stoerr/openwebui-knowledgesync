# OpenWebUI Knowledge Sync

A command-line tool to synchronize local directories with [OpenWebUI](https://docs.openwebui.com/)
knowledge bases: use an AI for your local knowledge without uploading files to a public service.

Each directory can be synchronized to its own knowledge base (one on one relationship!), 
keeping your knowledge collections in sync with your local file system automatically. 
I've got several directories with knowledge files where I write down
stuff, and a cron job runs this tool periodically to keep the corresponding knowledge bases up to date.

I developed that since I need to query knowledge I have in markdown files which
I don't want or cannot upload into public services like an OpenAI GPT / assistant but rather want to keep only locally.
For that OpenWebUI is great, but having to drag and drop files into the web interface is cumbersome, but, as of
7/25, OpenWebUI does not have tool to support synchronizing directories with knowledge bases anymore.
I'm using OpenWebUI together with local models running on [LM Studio](https://lmstudio.ai/).

Caution: this is vibe-coded in the sense that I wrote the specifications but hardly even studied the code, so this is not
representative for my professional coding skills. :-) I just needed something quick that works good enough. Even most of
this README is vibe-coded.

## Features

- 🔄 **Directory-to-Knowledge Base Sync**: Each directory synchronizes to its own knowledge base
- 📁 **Multi-directory Support**: Synchronize different directories to different knowledge bases
- 🎯 **Regex filtering**: Include only files matching your specified pattern
- 🔍 **Hash verification**: Uses SHA256 to detect file changes and avoid duplicate uploads
- 💾 **Smart deduplication**: Reuses existing files with matching content instead of re-uploading
- ⚡ **Zero dependencies**: Pure Node.js implementation, no external packages required
- 🛡️ **Error handling**: Comprehensive validation and cleanup on failures

## Installation

Clone the repository and link the script to your PATH for easy access,
assuming you have a `bin` directory in your home folder:

```bash
git clone https://github.com/your-username/openwebui-knowledgesync.git
ln -s openwebui-knowledgesync/openwebui-knowledgesync $HOME/bin/openwebui-knowledgesync
```

## Setup Process

### 1. Find Your Knowledge Base ID

First, discover the ID of the knowledge base you want to sync with:

```bash
openwebui-knowledgesync listkb
```

This will show all available knowledge bases with their IDs, names, and descriptions. Copy the ID of the knowledge base
you want to use.

### 2. Create Configuration File

In each directory you want to synchronize, create a `.open-webui-knowledgesync` configuration file:

```bash
knowledge_base_id=your-knowledge-base-id-from-step-1
open_webui_api_key=your-api-key
open_webui_api_url=http://localhost:3000
kbdir_id=unique-directory-identifier
fileregex=\.(md|txt|pdf|docx?)$
```

An open_webui_api_key can be created in OpenWebUI under users settings -> account -> API keys.

### 3. Choose a Unique Directory Identifier

The `kbdir_id` must be unique for each directory you synchronize. This identifier:

- Distinguishes files from different directories in the same knowledge base
- Should be descriptive (e.g., `project-docs`, `user-manual`, `api-specs`)
- Can contain letters, numbers, hyphens, and underscores
- Must be different for each directory, even if syncing to different knowledge bases

## Configuration Parameters

| Parameter            | Required | Description                             | Example                                |
|----------------------|----------|-----------------------------------------|----------------------------------------|
| `knowledge_base_id`  | Yes      | Target knowledge base ID from OpenWebUI | `a836f4b9-2ce6-4d30-bd28-ebd56c59eab3` |
| `open_webui_api_key` | Yes      | Your OpenWebUI API authentication key   | `sk-1234567890abcdef...`               |
| `open_webui_api_url` | No       | OpenWebUI instance URL                  | `http://localhost:3000` (default)      |
| `kbdir_id`           | Yes      | Unique identifier for this directory    | `docs`, `project-x`, etc.              |
| `fileregex`          | No       | Regex pattern for files to include      | `\.(md\|txt\|pdf)$`                    |

## File Path Encoding

Files are uploaded with encoded paths that preserve the directory structure:

- Directory separators (`/` or `\`) are replaced with `%%`
- The `kbdir_id` is prefixed to identify the source directory
- Example: `docs/api/endpoints.md` becomes `project-docs%%docs%%api%%endpoints.md`

This encoded format is visible in the OpenWebUI interface, making it easy to identify which directory and path each file
came from.

## Usage

### Commands

```bash
# Synchronize current directory with knowledge base. This is the main command, the rest is for setup / debugging.
openwebui-knowledgesync sync

# List all available knowledge bases
openwebui-knowledgesync listkb

# List all uploaded files
openwebui-knowledgesync listfiles

# List files in a specific knowledge base
openwebui-knowledgesync listkbfiles

# Download a file by ID to stdout
openwebui-knowledgesync download <file-id>
```

### Basic Workflow

1. **Discover**: Use `listkb` to find your knowledge base ID
2. **Setup**: Create configuration file in your directory with unique `kbdir_id`
3. **Sync**: Run `openwebui-knowledgesync sync` to synchronize files. Can be run periodically (e.g. with cron) or manually.
4. **Iterate**: Modify files locally and re-run sync as needed

### Example Setup

```bash
# Navigate to your documentation directory
cd /path/to/your/project-docs

# Find knowledge base ID
openwebui-knowledgesync listkb

# Create configuration with unique kbdir_id
cat > .open-webui-knowledgesync << EOF
knowledge_base_id=a836f4b9-2ce6-4d30-bd28-ebd56c59eab3
open_webui_api_key=sk-your-api-key-here
open_webui_api_url=http://localhost:3000
kbdir_id=project-docs
fileregex=\.(md|txt)$
EOF

# Perform initial sync
openwebui-knowledgesync sync
```

For a second directory:

```bash
# Navigate to another directory
cd /path/to/your/api-specs

# Create configuration with different kbdir_id
cat > .open-webui-knowledgesync << EOF
knowledge_base_id=b947c3a8-8e71-5f41-ce39-f68ef7c4fb6d
open_webui_api_key=sk-your-api-key-here
open_webui_api_url=http://localhost:3000
kbdir_id=api-specs
fileregex=\.(json|yaml|md)$
EOF

# Sync this directory to a different knowledge base
openwebui-knowledgesync sync
```

## How It Works

### Multi-Directory Support

- Each directory maintains its own configuration file
- Files are prefixed with `kbdir_id` to avoid conflicts
- Different directories can sync to the same or different knowledge bases
- The tool only manages files with its specific `kbdir_id` prefix

### Synchronization Process

1. **Scan local files** matching the regex pattern
2. **Fetch knowledge base** content and filter by `kbdir_id`
3. **Compare hashes** to identify changes
4. **Remove outdated files** belonging to this directory
5. **Upload new/changed files** with deduplication
6. **Add files to collection** and verify success

## Requirements

- Node.js (version 12 or higher)
- Access to an OpenWebUI instance
- Valid API key with knowledge base permissions

## Troubleshooting

### Common Issues

**Configuration not found**

```bash
Configuration file not found: /path/to/.open-webui-knowledgesync
```

Ensure the configuration file exists in your current directory.

**Hash mismatch warnings**

```bash
Hash mismatch for uploaded file: local vs uploaded hash different
```

This may indicate file encoding issues. Ensure files are valid UTF-8.

**Upload failures**

```bash
Upload failed: No file ID in response
```

Check your API key permissions and OpenWebUI instance connectivity.

### Debug Mode

Enable debug output by modifying the script:

```javascript
const debug = true; // Set to true for debug output
```

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Related Projects

- [OpenWebUI](https://github.com/open-webui/open-webui) - The main OpenWebUI project
- [OpenWebUI Documentation](https://docs.openwebui.com/) - Official documentation

## Bugs

It seems the sha256 calculation of OpenWebUI is somehow different than
this in some cases. That means the file will be uploaded again each time.
I don't know how that can be fixed at the moment.

---

**Note**: This tool is not affiliated with the OpenWebUI project in any way.
