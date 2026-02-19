---
name: discover
description: "Scan a repo for documents to organize. Finds .md, .docx, .txt, .pdf files and presents them for ingestion. Use when the user says 'discover', 'scan this repo', or 'find documents'."
---

# Scribe: Discover — Repository Document Scanner

Scans a repository for documents the user might want to organize. Presents a summary and lets the user choose what to ingest.

## Workflow

### 1. Scan the Repository

Search the current repo for documents:
- `**/*.md`
- `**/*.docx`
- `**/*.txt`
- `**/*.pdf`

**Exclude:** `node_modules/`, `.git/`, `claude-notes/` (our own output), `venv/`, `__pycache__/`, `.claude/`, `build/`, `dist/`

### 2. Present Summary

Group findings by directory and format:

```
Found 23 documents across this repo:

📁 docs/ (8 files)
  - architecture-overview.md
  - api-reference.md
  - ...

📁 meetings/ (5 files)
  - 2026-02-15-retrospective.md
  - 2026-02-18-planning.md
  - ...

📁 analysis/ (10 files)
  - threat-assessment.docx
  - risk-matrix.pdf
  - ...
```

Include:
- Total document count
- Breakdown by directory
- Breakdown by format
- Notable patterns (e.g., "meetings/ appears to contain meeting notes with dates")

### 3. Let the User Choose

Ask which documents or directories to ingest:
- "Ingest all" — process everything
- "Ingest docs/ and meetings/" — specific directories
- "Skip analysis/" — exclude specific areas
- "Just the markdown files" — filter by format

### 4. Hand Off to Ingest

Once the user selects, process using the ingest skill's workflow. The discover skill is the front door; ingest does the actual work.

## Quick Invocation

- `/scribe:discover` — scan current repo
- "scribe, scan this repo" — discover mode
- "scribe, what documents are in this project?" — discover mode
- "find all the notes and docs" — discover mode
