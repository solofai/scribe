---
name: ingest
description: "Bulk import existing documents (.md, .docx, .txt, .pdf) into structured, indexed notes. Use when the user says 'ingest', 'organize these docs', or provides file paths."
---

# Scribe: Ingest — Document Import and Organization

Bulk import from existing documents in mixed formats. Reads, parses, and organizes content into structured notes with cross-references.

## Output Directory

Notes go into `claude-notes/` in the current project directory. See the dictation skill for directory structure details.

## Workflow

### 0. Load User Profile

If `references/user-profile.md` exists, read it first for style preferences.

### 1. Accept Input

Accept file paths, directory paths, or glob patterns from the user. Examples:
- "ingest docs/retrospective.md"
- "ingest all markdown files in meetings/"
- "ingest analysis/*.docx"

### 2. Handle Mixed Formats

| Format | How to read |
|--------|------------|
| `.md` | Read directly |
| `.txt` | Read directly |
| `.docx` | Convert with `textutil -convert txt <file>` (macOS). If unavailable, try `pandoc -t plain <file>`. |
| `.pdf` | Use the Read tool (supports PDF reading) |

### 3. Parse Each Document

For each document:
- Identify distinct topics and key ideas
- Note the source file and author (if identifiable from content or filename)
- Extract actionable items, decisions, findings, and open questions
- **Preserve source attribution:** every note references its source document

### 4. Build Idea Landscape and Merge

Read `claude-notes/INDEX.md`. Apply the same merge heuristic as dictation:
- Merge when content extends an existing note
- Create new when conceptually independent
- Cross-reference related notes from different source documents

### 5. Write Notes

See `references/note-schema.md` for format. Additional requirements for ingested docs:
- **Source attribution** in Context section: "Ingested from `path/to/file.ext`"
- **Author attribution** if identifiable: note in Context or tag with `#author-name`
- The Idea section should **distill**, not copy — transform raw document content into structured, cross-referenced prose

### 6. Update Index and Respond

Update `claude-notes/INDEX.md`. The `Source` column shows the original filename.

Respond with:
- Documents processed (count and names)
- Notes created vs. updated
- Topics identified across all documents
- Cross-references found between documents
- Files that couldn't be processed (format issues, empty, etc.)

## Multi-Author Support

When ingesting from multiple authors:
- Note the author in Context if identifiable
- Tag with `#author-name`
- When merging ideas from different authors on the same topic, preserve both perspectives

## Quick Invocation

- `/scribe:ingest [path]` — ingest specific files or directory
- "scribe, ingest docs/" — ingest a directory
- "organize these files" — ingest mode
