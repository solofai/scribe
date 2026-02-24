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

**No exceptions.** Always use `claude-notes/` in the current working directory, even outside a git repo. Never use `~/.claude/notes/`.

**First run:** If `claude-notes/` or `claude-notes/INDEX.md` does not exist, create them.

## Workflow

### 0. Load or Initialize the Working User Profile

Use `~/.scribe/user-profile.md` as the active profile for personalization.

- If `~/.scribe/user-profile.md` exists, read it first.
- If missing, create a neutral scaffold with `# Communication Profile: User`, `Last Updated`, `Sessions Analyzed: 0`, `**Profile Format:** v2`, and the standard profile sections (including the Candidate Patterns table).
- Use `references/user-profile.md` only as a public example for section quality and pattern granularity.
- If you copy anything from the public example, place it under `## Candidate Patterns (Need More Evidence)` with a `[seed-example]` marker, not as confirmed behavior.
- Never overwrite `references/user-profile.md` during normal runs; treat it as the public baseline example.
- **v1 migration:** If the profile exists but lacks `**Profile Format:** v2`, migrate it per the v1→v2 procedure in `shared/references/session-tracking.md`.

### 0b. Reconciliation Pass

Run the reconciliation procedure from `shared/references/session-tracking.md`:

1. Glob `~/.scribe/sessions/*.md`.
2. For each candidate in the profile's Candidate Patterns table, grep session files for its slug and recount.
3. If any candidate now has 2+ sessions of evidence, promote it to the appropriate main section.
4. Update the profile if any changes were made.

This catches promotions lost to concurrent writes or interrupted sessions.

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

Use the two-phase session tracking protocol from `shared/references/session-tracking.md`:

**Phase 1 — Write Session Record:**
- Create `~/.scribe/sessions/` if it doesn't exist.
- Write a session file named `{YYYYMMDDTHHMMSS}-dictation-{5-random}.md`.
- Record observed patterns (tagged with `[idea-structure]`, `[priority-signal]`, `[style-pref]`, `[behavior]`), explicit statements, and candidate evidence with canonical slugs.
- Session files are write-once, never modified.

**Phase 2 — Update Profile with Evidence Counting:**
- For each observed pattern, determine its canonical slug.
- Grep across all files in `~/.scribe/sessions/` for each slug to count distinct sessions.
- Apply promotion rules:
  - **Explicit statement** in this session → promote immediately to the appropriate main section.
  - **2+ sessions** containing the slug → promote from Candidate Patterns to the appropriate main section.
  - **<2 sessions** → add or update the row in the Candidate Patterns table (update Sessions count, Last Seen date).
- Update `Last Updated`, `Sessions Analyzed`, and `Profile Evolution Log`.
- Keep profile updates additive; do not delete prior observations.
- Best-effort git commit of session file + profile to `~/.scribe` repo.

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
