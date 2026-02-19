# Note Schema Reference — Scribe

## Note File Format

Every note in `claude-notes/` uses this structure:

```markdown
# [Topic Title]

**Created:** YYYY-MM-DD
**Updated:** YYYY-MM-DD
**Tags:** #tag1 #tag2 #tag3
**Source:** dictation | filename.ext | discovered

## Context
_YYYY-MM-DD_ — Why this came up, what prompted the thinking. For ingested docs, note the source file and author if known.

## Idea
_YYYY-MM-DD_ — The core concept, observation, or proposal. Distill source material into clear, structured prose.

## Impact
_YYYY-MM-DD_ — What this affects. Which components, decisions, deliverables.

## Related
- `other-note.md` — how it connects
- `source/path/to/original.md` — original document if ingested

## Next Steps
- [ ] _YYYY-MM-DD_ — Action item or open question

## Source Log
> **YYYY-MM-DD:** Original raw dictation text / "Ingested from path/to/file.md"
```

## Timestamp Rules

- Every section entry prefixed with italic ISO 8601 timestamp
- When merging: add below existing with new timestamp, never replace
- `Updated` reflects most recent edit; `Created` never changes

## When Entries Contradict

Keep both with timestamps. The temporal record shows thinking evolution. Never delete earlier entries.

## Index Format

`claude-notes/INDEX.md`:

```markdown
# Scribe Notes Index

Structured notes captured via dictation, document ingestion, and repo discovery.

| File | Summary | Tags | Source | Created | Updated |
|------|---------|------|--------|---------|---------|
| `topic.md` | One-line summary | `#tags` | dictation / filename | date | date |
```

## File Naming

- Kebab-case: `threat-model-retrospective.md`
- Descriptive but concise
- No date prefixes — timestamps live inside the file

## Tag Vocabulary

Common tags:
- `#idea`, `#finding`, `#decision`, `#action`, `#retrospective`
- `#architecture`, `#threat`, `#workflow`
- Author: `#author-name`
- Source: `#from-meeting`, `#from-analysis`, `#from-dictation`
