> 中文文档请参阅 [README.md](./README.md)

# zhipu-complaint

Claude Code plugin for consumer rights complaints against Zhipu AI (智谱华章科技有限公司).

## What This Does

This plugin turns Claude Code into a conversational complaint letter assistant. Through a structured interview, Claude gathers your facts, applies relevant Chinese consumer protection law, and generates a ready-to-send Markdown complaint letter. Supports iterative refinement with versioned output.

## Installation

```bash
# Install from GitHub
/plugins add https://github.com/yonh/zhipu-complaint

# Or install locally
/plugins add /path/to/zhipu-complaint
```

## Usage

| Command | Language | Description |
|---------|----------|-------------|
| `/zhipu-complaint` | Chinese (default) | Start a new complaint session guided by Claude |
| `/zhipu-complaint-update` | Chinese (default) | Add facts, update demands, or refine an existing draft |
| `/zhipu-complaint-en` | English | Same as above in English |
| `/zhipu-complaint-update-en` | English | Same as above in English |

## What to Prepare

Before starting, have the following information ready:

- **Key dates and timeline** — When did you subscribe? When did the issue start?
- **What was promised** — Screenshots of announcements, emails, or UI showing what you were promised
- **What actually happened** — How the reality differs from the promise
- **Your specific demands** — Refund, restore rights, compensation, etc.
- **Evidence on hand** — Receipts, screenshots, support chat logs, etc.

## Output Format

Generated complaint letters are:

- **Markdown format** — suitable for email, print, or social media posts
- **Versioned** — each revision is labeled (v1, v2, v3...)
- **Change-tracked** — updates include a summary of what changed

## Legal Basis

This plugin references Chinese consumer protection laws including:

- 《消费者权益保护法》(Consumer Rights Protection Law)
- 《民法典》合同编 (Civil Code — Contract Section)
- 《网络交易监督管理办法》(Online Transaction Supervision Measures)

**Important:** Users should verify all legal citations for accuracy before submitting formal complaints to authorities.

## License

MIT
