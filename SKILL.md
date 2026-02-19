---
name: notion-md-converter
description: Convert Markdown content to Notion blocks format. Use when creating Notion pages from Markdown, converting documentation to Notion, or batch importing Markdown files to Notion. Triggers on "markdown to notion", "md to notion", "convert markdown", "notion blocks".
---

# Notion Markdown Converter

**Version:** 2.0.0 | **Author:** OpenClaw | **Homepage:** https://github.com/Charpup/openclaw-notion-md-converter

Markdown 到 Notion blocks 格式转换器，支持丰富的 Markdown 语法到 Notion API 块的转换。

## 功能特性

- 📝 **完整语法支持** - 支持标题、列表、代码块、表格等
- 🖼️ **媒体处理** - 自动处理图片和附件上传
- 🔗 **链接转换** - 支持内链和外链转换
- 📊 **表格支持** - Markdown 表格转 Notion 表格
- 🎨 **样式保留** - 粗体、斜体、删除线等格式

## 支持的转换

| Markdown | Notion Block |
|----------|--------------|
| `# H1` | heading_1 |
| `## H2` | heading_2 |
| `### H3` | heading_3 |
| `- item` | bulleted_list_item |
| `1. item` | numbered_list_item |
| `- [ ] task` | to_do |
| `> quote` | quote |
| `` `code` `` | inline code |
| ` ``` ` | code block |
| `---` | divider |
| `| table |` | table |
| `![img](url)` | image |

## 安装

```bash
npm install notion-md-converter
```

## 配置

环境变量：
```bash
export NOTION_TOKEN="your-notion-integration-token"
```

## 使用方法

### 作为库使用

```javascript
const { MarkdownToNotion } = require('notion-md-converter');

const converter = new MarkdownToNotion({
  notionToken: process.env.NOTION_TOKEN
});

// 转换 Markdown 字符串
const blocks = converter.convert(`
# Hello World

This is a **bold** and *italic* text.

- Item 1
- Item 2
`);

console.log(blocks);
```

### CLI 使用

```bash
# 转换文件并输出到 stdout
notion-md-converter convert input.md

# 转换并创建 Notion 页面
notion-md-converter create-page --file input.md --parent PAGE_ID

# 批量转换目录
notion-md-converter batch ./docs --output ./notion-blocks
```

## 示例

### 示例 1: 基础转换

```javascript
const { MarkdownToNotion } = require('notion-md-converter');

const markdown = `
# Project Documentation

## Overview

This project provides **powerful** tools for developers.

### Features

- Fast execution
- Easy to use
- Well documented

## Code Example

\`\`\`javascript
const result = await process(data);
console.log(result);
\`\`\`

## Table

| Feature | Status |
|---------|--------|
| Auth    | ✅     |
| API     | ✅     |
| UI      | 🚧     |
`;

const converter = new MarkdownToNotion();
const blocks = converter.convert(markdown);

console.log(JSON.stringify(blocks, null, 2));
```

### 示例 2: 创建 Notion 页面

```javascript
const { NotionPageCreator } = require('notion-md-converter');

const creator = new NotionPageCreator({
  notionToken: process.env.NOTION_TOKEN
});

async function createDocPage() {
  const page = await creator.createFromFile(
    './README.md',
    {
      parent: { page_id: 'your-parent-page-id' },
      title: 'Project Documentation'
    }
  );
  
  console.log(`Created page: ${page.url}`);
}

createDocPage();
```

### 示例 3: 批量导入

```bash
#!/bin/bash
# 批量导入文档目录到 Notion

DOCS_DIR="./documentation"
PARENT_PAGE="your-parent-page-id"

for file in "$DOCS_DIR"/**/*.md; do
  filename=$(basename "$file" .md)
  echo "Importing: $filename"
  
  notion-md-converter create-page \
    --file "$file" \
    --parent "$PARENT_PAGE" \
    --title "$filename"
done
```

## API 参考

### MarkdownToNotion

主转换器类。

```javascript
const converter = new MarkdownToNotion(options);
```

**选项：**

| 选项 | 类型 | 描述 |
|------|------|------|
| `notionToken` | string | Notion Integration Token |
| `uploadImages` | boolean | 是否上传本地图片 |
| `strictMode` | boolean | 遇到未知语法时抛出错误 |

**方法：**

```javascript
// 转换 Markdown 字符串
converter.convert(markdown: string): Block[]

// 转换 Markdown 文件
converter.convertFile(filePath: string): Block[]

// 批量转换
converter.convertFiles(filePaths: string[]): Block[][]
```

### NotionPageCreator

创建 Notion 页面的高级 API。

```javascript
const creator = new NotionPageCreator(options);
```

**方法：**

```javascript
// 从 Markdown 字符串创建页面
creator.create(markdown: string, options: CreateOptions): Promise<Page>

// 从文件创建页面
creator.createFromFile(filePath: string, options: CreateOptions): Promise<Page>

// 批量创建页面
creator.batchCreate(files: string[], options: BatchOptions): Promise<Page[]>
```

## 高级用法

### 自定义块处理器

```javascript
const converter = new MarkdownToNotion();

// 注册自定义处理器
converter.registerHandler('customDirective', (node) => {
  return {
    type: 'callout',
    callout: {
      rich_text: [{ text: { content: node.content } }],
      icon: { emoji: '💡' }
    }
  };
});
```

### 处理图片

```javascript
const converter = new MarkdownToNotion({
  uploadImages: true,
  imageHandler: async (imagePath) => {
    // 自定义图片上传逻辑
    const url = await uploadToCDN(imagePath);
    return url;
  }
});
```

## 错误处理

```javascript
try {
  const blocks = converter.convert(markdown);
} catch (error) {
  if (error instanceof ParseError) {
    console.error('Parse error:', error.message);
  } else if (error instanceof NotionAPIError) {
    console.error('Notion API error:', error.message);
  }
}
```

## 与 notion-mcp-wrapper 集成

```javascript
const { NotionMCPWrapper } = require('notion-mcp-wrapper');
const { MarkdownToNotion } = require('notion-md-converter');

const wrapper = new NotionMCPWrapper();
const converter = new MarkdownToNotion();

async function createPageFromMarkdown(markdown, parentId) {
  const blocks = converter.convert(markdown);
  
  await wrapper.execute('appendBlocks', {
    block_id: parentId,
    children: blocks
  });
}
```

## License

MIT
