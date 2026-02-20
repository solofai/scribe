---
name: dictation
description: "Organize raw ideas — spoken or typed — into structured, indexed notes. Use when the user says 'take dictation', 'scribe', or pastes unstructured text."
---

# Scribe: Dictation - Idea Organizer

Parse unstructured input (speech-to-text, typed ramblings, braindumps), identify distinct topics, merge into existing notes or create new ones, and maintain a searchable index. Input may be messy, fragmented, or imperfect — that's expected.

## Output Directory

Notes go into `claude-notes/` in the current project directory to avoid collision with existing project files.

```
project-root/
|- claude-notes/
|  |- INDEX.md
|  |- topic-one.md
|  \- ...
|- existing-project-files/
\- ...
```

**Exception:** If invoked outside a project (no git repo), or if the user explicitly asks, notes go to `~/.claude/notes/`.

**First run:** If `claude-notes/` or `claude-notes/INDEX.md` does not exist, create them.

## Workflow

### 0. Load or Initialize the Working User Profile

Use `~/.scribe/user-profile.md` as the active profile for personalization.

- If `~/.scribe/user-profile.md` exists, read it first.
- If missing, create a neutral scaffold with `# Communication Profile: User`, `Last Updated`, `Sessions Analyzed: 0`, and the standard profile sections.
- Use `references/user-profile.md` only as a public example for section quality and pattern granularity.
- If you copy anything from the public example, place it under `## Candidate Patterns (Need More Evidence)` with a `[seed-example]` marker, not as confirmed behavior.
- Never overwrite `references/user-profile.md` during normal runs; treat it as the public baseline example.

### 1. Parse the Dictation

- Read raw text carefully.
- Identify distinct ideas or topics (may be 1 or many).
- Separate unrelated thoughts into individual note candidates.
- **Key insight extraction:** Users often bury the core idea partway through. Scan for transition phrases: "so what would be useful is...", "the idea is...", "it'd be great if..."
- **Priority from tone:**
  - High: excitement, detailed elaboration, connecting to multiple concepts.
  - Low: "probably not worth exploring", hedging.
  - Meta/feedback: "I like how you're doing X" - act on it, do not create a note.

### 2. Build Idea Landscape and Check Existing Notes

Read `claude-notes/INDEX.md`. Reason about relationships between existing notes.

**Merge heuristic:**
- **Merge** when new content extends, refines, or adds a dimension to an existing note.
- **Create new** when content is conceptually independent even if it shares tags.
- Ask: "Does this make the existing note more complete, or does it stand on its own?"

### 3. Write or Update Notes

See `references/note-schema.md` for the full format. Mandatory quality standards:
- Every section filled in - no empty or placeholder sections.
- Idea section distills dictation into clear prose - not just echoed speech.
- Related links verified (files exist, cross-references are real).
- Next Steps are concrete and actionable.
- Timestamps on creation and every update.

### 4. Update the Index

**Always** update `claude-notes/INDEX.md` after every change. Each entry includes:

| File | Summary | Tags | Source | Created | Updated |
|------|---------|------|--------|---------|---------|

### 5. Learn and Update the Working User Profile

Continuously adapt the active profile in `~/.scribe/user-profile.md`.

- Capture explicit preferences, recurring communication patterns, and priority signals from this session.
- Promote a new pattern to the main sections only when:
  - The user states it explicitly in this session, or
  - It has appeared in at least 2 sessions.
- Add uncertain patterns to `## Candidate Patterns (Need More Evidence)` instead of treating them as stable.
- Update `Last Updated` and `Sessions Analyzed`.
- Append a concise entry to `## Profile Evolution Log` with date and evidence summary.
- Keep profile updates additive; do not delete prior observations.

### 6. Respond

Brief summary:
- Topics identified (count).
- Notes created vs. updated (with filenames).
- Cross-references found.
- Priority level detected.
- Profile updates applied (or none).
- Ambiguous points needing clarification.

## Merge Rules

- Additive only - never delete or overwrite existing note content.
- New content gets a fresh timestamp.
- If entries contradict, keep both with timestamps - temporal record matters.
- Append raw dictation to the Source Log.

## File Naming

- Kebab-case: `threat-model-retrospective.md`
- Descriptive but concise.
- No date prefixes - timestamps live inside the file.

## Quick Invocation

- `/scribe:dictation` or "take dictation"
- Or just paste raw text
