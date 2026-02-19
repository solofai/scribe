---
name: dictation
description: "Take dictated speech-to-text ramblings and organize them into structured, indexed notes. Use when the user says 'take dictation', 'scribe', or pastes raw speech-to-text content."
---

# Scribe: Dictation — Speech-to-Text Note Organizer

Real-time speech-to-text capture. Parses rambling dictation, identifies distinct topics, merges into existing notes or creates new ones, and maintains a searchable index.

## Output Directory

Notes go into `claude-notes/` in the current project directory to avoid collision with existing project files.

```
project-root/
├── claude-notes/
│   ├── INDEX.md
│   ├── topic-one.md
│   └── ...
├── existing-project-files/
└── ...
```

**Exception:** If invoked outside a project (no git repo), or if the user explicitly asks, notes go to `~/.claude/notes/`.

**First run:** If `claude-notes/` or `claude-notes/INDEX.md` doesn't exist, create them.

## Workflow

### 0. Load User Profile

If `references/user-profile.md` exists in this skill's directory, read it first. It contains communication patterns, priority signals, and style preferences.

### 1. Parse the Dictation

- Read raw text carefully
- Identify distinct ideas or topics (may be 1 or many)
- Separate unrelated thoughts into individual note candidates
- **Key insight extraction:** Users often bury the core idea partway through. Scan for transition phrases: "so what would be useful is...", "the idea is...", "it'd be great if..."
- **Priority from tone:**
  - High: excitement, detailed elaboration, connecting to multiple concepts
  - Low: "probably not worth exploring", hedging
  - Meta/feedback: "I like how you're doing X" — act on it, don't create a note

### 2. Build Idea Landscape and Check Existing Notes

Read `claude-notes/INDEX.md`. Reason about relationships between existing notes.

**Merge heuristic:**
- **Merge** when new content extends, refines, or adds a dimension to an existing note
- **Create new** when content is conceptually independent even if it shares tags
- Ask: "Does this make the existing note more complete, or does it stand on its own?"

### 3. Write or Update Notes

See `references/note-schema.md` for the full format. Mandatory quality standards:
- Every section filled in — no empty or placeholder sections
- Idea section distills dictation into clear prose — not just echoed speech
- Related links verified (files exist, cross-references are real)
- Next Steps are concrete and actionable
- Timestamps on creation and every update

### 4. Update the Index

**Always** update `claude-notes/INDEX.md` after every change. Each entry includes:

| File | Summary | Tags | Source | Created | Updated |
|------|---------|------|--------|---------|---------|

### 5. Respond

Brief summary:
- Topics identified (count)
- Notes created vs. updated (with filenames)
- Cross-references found
- Priority level detected
- Ambiguous points needing clarification

## Merge Rules

- Additive only — never delete or overwrite existing content
- New content gets a fresh timestamp
- If entries contradict, keep both with timestamps — temporal record matters
- Append raw dictation to the Source Log

## File Naming

- Kebab-case: `threat-model-retrospective.md`
- Descriptive but concise
- No date prefixes — timestamps live inside the file

## Quick Invocation

- `/scribe:dictation` or "take dictation"
- Or just paste raw text
