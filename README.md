# Scribe

A [Claude Code](https://claude.ai/code) plugin for knowledge capture. Turns speech-to-text dictation, existing documents, and scattered repo files into structured, indexed, cross-referenced notes.

## Skills

| Skill | Command | What it does |
|-------|---------|-------------|
| **Dictation** | `/scribe:dictation` | Parse raw speech-to-text, identify topics, merge into existing notes or create new ones |
| **Ingest** | `/scribe:ingest [path]` | Bulk import `.md`, `.docx`, `.txt`, `.pdf` files into structured notes |
| **Discover** | `/scribe:discover` | Scan a repo for documents, present a summary, hand off to ingest |

## Install

```bash
# Option 1: Load for a single session
claude --plugin-dir /path/to/scribe

# Option 2: Add as a local marketplace, then install
/plugin marketplace add /path/to/scribe
/plugin install scribe@scribe
```

## How it works

All notes go into `claude-notes/` in your project directory to avoid colliding with existing files. Each note follows a consistent schema:

```
claude-notes/
|- INDEX.md          # Searchable table of all notes
|- topic-one.md      # Structured note with Context, Idea, Impact, Related, Next Steps
|- topic-two.md
\- ...
```

Notes are **additive** — new content gets timestamped and appended, never overwrites. Contradictions are preserved with timestamps so the thinking evolution is visible.

### Merge heuristic

When new content arrives, scribe checks the existing index and decides:
- **Merge** when content extends, refines, or adds a dimension to an existing note
- **Create new** when content is conceptually independent, even if it shares tags

### User profile

Scribe learns your communication patterns over time. The runtime profile lives at `~/.scribe/user-profile.md` (private, never committed to project repos). The `references/user-profile.md` files in this repo are public examples showing the profile format.

## Note schema

Every note uses this structure:

```markdown
# Topic Title

**Created:** YYYY-MM-DD
**Updated:** YYYY-MM-DD
**Tags:** #tag1 #tag2
**Source:** dictation | filename.ext | discovered

## Context
## Idea
## Impact
## Related
## Next Steps
## Source Log
```

See `skills/dictation/references/note-schema.md` for the full specification.

## License

MIT
