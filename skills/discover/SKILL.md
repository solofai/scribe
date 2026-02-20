---
name: discover
description: "Scan a repo for documents to organize. Find .md, .docx, .txt, .pdf files and present them for ingestion. Use when the user says 'discover', 'scan this repo', or 'find documents'."
---

# Scribe: Discover - Repository Document Scanner

Scan a repository for documents the user might want to organize. Present a summary and let the user choose what to ingest.

## Workflow

### 0. Load or Initialize the Working User Profile

Use `~/.scribe/user-profile.md` as the active profile for personalization.

- If `~/.scribe/user-profile.md` exists, read it first.
- If missing, create a neutral scaffold with `# Communication Profile: User`, `Last Updated`, `Sessions Analyzed: 0`, and the standard profile sections.
- Use `references/user-profile.md` only as a public example for section quality and pattern granularity.
- If you copy anything from the public example, place it under `## Candidate Patterns (Need More Evidence)` with a `[seed-example]` marker, not as confirmed behavior.
- Never overwrite `references/user-profile.md` during normal runs; treat it as the public baseline example.

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

[docs/] (8 files)
  - architecture-overview.md
  - api-reference.md
  - ...

[meetings/] (5 files)
  - 2026-02-15-retrospective.md
  - 2026-02-18-planning.md
  - ...

[analysis/] (10 files)
  - threat-assessment.docx
  - risk-matrix.pdf
  - ...
```

Include:
- Total document count.
- Breakdown by directory.
- Breakdown by format.
- Notable patterns (e.g., "meetings/ appears to contain meeting notes with dates").

### 3. Let the User Choose

Ask which documents or directories to ingest:
- "Ingest all" - process everything.
- "Ingest docs/ and meetings/" - specific directories.
- "Skip analysis/" - exclude specific areas.
- "Just the markdown files" - filter by format.

### 4. Hand Off to Ingest

Once the user selects, process using the ingest skill's workflow. The discover skill is the front door; ingest does the actual work.

### 5. Learn and Update the Working User Profile

Apply lightweight profile learning from discover behavior.

- Capture preferences inferred from selections and exclusions (what the user tends to include or skip).
- Promote stable patterns using the same evidence rule as dictation (explicit or repeated).
- Update `~/.scribe/user-profile.md` additively.

## Quick Invocation

- `/scribe:discover` - scan current repo
- "scribe, scan this repo" - discover mode
- "scribe, what documents are in this project?" - discover mode
- "find all the notes and docs" - discover mode
