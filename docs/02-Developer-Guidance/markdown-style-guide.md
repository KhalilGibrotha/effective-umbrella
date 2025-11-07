---
title: "Markdown Documentation Style Guide"
status: "Draft"
owner: "Server Engineering and Operations"
last_updated: 2025-11-06
version: "1.0"
---

# Markdown Documentation Style Guide

## Purpose
Standardize Markdown authoring for consistent readability, export quality, and compatibility across GitHub, SharePoint, and Pandoc builds.

## General Rules
| Rule | Description |
|------|--------------|
| Heading hierarchy | Start with one `#`, increase sequentially (`#`, `##`, `###`) |
| Line length | ≤ 120 characters |
| Lists | Use `-` for unordered, `1.` for ordered lists |
| Code blocks | Use fenced syntax with language hint (` ```yaml`) |
| Links | Use absolute paths or full GitHub URLs for reliability |
| Tables | Align columns with a single space |
| Metadata | YAML front matter required at top of each doc |
| Diagrams | Use fenced ` ```mermaid` blocks (no parentheses) |
| File naming | Lowercase with hyphens (e.g., `ansible-style-guide.md`) |

## Example Front Matter
```yaml
---
title: "Document Title"
status: "Draft"
owner: "Team Name"
last_updated: YYYY-MM-DD
version: "1.x"
---

## Recommended Tools

- markdownlint – for structure and spacing
- Prettier – for formatting tables and lists
- Pandoc – for rendering to Word, PDF, and HTML

Sample .markdownlint.yaml:

```yaml
default: true
MD013:
  line_length: 120
MD041: false
```

## References

- https://github.com/DavidAnson/markdownlint
- https://pandoc.org/MANUAL.html