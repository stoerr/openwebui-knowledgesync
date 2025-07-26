# OpenWebUI Knowledge Sync

A command-line tool to synchronize local files with OpenWebUI knowledge bases. Keep your knowledge collections in sync with your local file system automatically.

## Features

- 🔄 **Synchronization**: Automatically upload new/changed files and remove outdated ones
- 📁 **Directory-based**: Works with any directory structure, encoding paths with `%%` separators
- 🎯 **Regex filtering**: Include only files matching your specified pattern
- 🔍 **Hash verification**: Uses SHA256 to detect file changes and avoid duplicate uploads
- ⚡ **Zero dependencies**: Pure Node.js implementation, no external packages required

## Installation

Clone the repository and link the script to your PATH for easy access,
assuming you have a `bin` directory in your home folder:

```bash
git clone https://github.com/your-username/openwebui-knowledgesync.git
ln -s openwebui-knowledgesync/openwebui-knowledgesync $HOME/bin/openwebui-knowledgesync
```

## Configuration

Create a `.open-webui-knowledgesync` configuration file in your knowledge directory:

```bash
knowledge_base_id=your-knowledge-base-id
open_webui_api_key=your-api-key
open_webui_api_url=http://localhost:3000
kbdir_id=unique-directory-identifier
fileregex=\.(md|txt|pdf|docx?)$
```

### Configuration Parameters

| Parameter | Required | Description | Example |
|-----------|----------|-------------|---------|
| `knowledge_base_id` | Yes | Target knowledge base ID from OpenWebUI | `a836f4b9-2ce6-4d30-bd28-ebd56c59eab3` |
| `open_webui_api_key` | Yes | Your OpenWebUI API authentication key | `sk-1234567890abcdef...` |
| `open_webui_api_url` | No | OpenWebUI instance URL | `http://localhost:3000` (default) |
| `kbdir_id` | Yes | Unique identifier for this directory | `docs`, `project-x`, etc. |
| `fileregex` | No | Regex pattern for files to include | `\.(md\|txt\|pdf)$` |

## Usage

### Commands

```bash
# Synchronize current directory with knowledge base
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

1. **Setup**: Create configuration file in your knowledge directory
2. **Sync**: Run `openwebui-knowledgesync sync` to synchronize files
3. **Iterate**: Modify files locally and re-run sync as needed

### Example

```bash
# Navigate to your documentation directory
cd /path/to/your/docs

# Create configuration
cat > .open-webui-knowledgesync << EOF
knowledge_base_id=a836f4b9-2ce6-4d30-bd28-ebd56c59eab3
open_webui_api_key=sk-your-api-key-here
open_webui_api_url=http://localhost:3000
kbdir_id=my-docs
fileregex=\.(md|txt)$
EOF

# Perform initial sync
openwebui-knowledgesync sync
```

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

---

**Note**: This tool is not affiliated with the OpenWebUI project in any way.

