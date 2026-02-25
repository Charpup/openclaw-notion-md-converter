# Notion Markdown Converter

[![Version](https://img.shields.io/badge/version-2.1.0-blue.svg)](https://github.com/Charpup/openclaw-notion-md-converter/releases)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-skill-purple.svg)](https://openclaw.ai)

Convert Markdown content to Notion blocks format. Supports full Markdown syntax including headings, lists, code blocks, tables, images, and inline formatting.

## Features

- Full Markdown syntax → Notion API blocks conversion
- Batch import multiple files
- Custom block handlers
- CLI and library API
- Integration with notion-mcp-wrapper

## Installation

```bash
npm install notion-md-converter
export NOTION_TOKEN="your-notion-integration-token"
```

## Usage

### Library

```javascript
const { MarkdownToNotion } = require('notion-md-converter');
const converter = new MarkdownToNotion();
const blocks = converter.convert('# Hello\n\nThis is **bold** text.');
```

### CLI

```bash
# Convert file to Notion blocks (stdout)
notion-md-converter convert input.md

# Create Notion page from Markdown file
notion-md-converter create-page --file input.md --parent PAGE_ID

# Batch import directory
notion-md-converter batch ./docs --output ./notion-blocks
```

## Supported Conversions

| Markdown | Notion Block |
|----------|-------------|
| `# H1` | heading_1 |
| `- item` | bulleted_list_item |
| `1. item` | numbered_list_item |
| `- [ ] task` | to_do |
| `` ```code``` `` | code |
| `\| table \|` | table |
| `![img](url)` | image |

## Notion Skill Ecosystem

| Skill | Role | Use When |
|-------|------|---------|
| **notion-mcp-wrapper** | Connection layer: health monitoring, auto-reconnect, REST fallback | Need reliable Notion API access |
| **notion-md-converter** (this skill) | Format conversion: Markdown → Notion blocks | Importing docs, creating pages from Markdown |
| **notion-journal-skill** | Journal automation: memory scanning, content aggregation | Generating daily work journals |

**Combination example**: `notion-md-converter` converts content → `notion-journal-skill` creates journal entry

## License

MIT
