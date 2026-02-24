---
name: discover
description: "Scan a repo for documents to organize. Find .md, .docx, .txt, .pdf files and present them for ingestion. Use when the user says 'discover', 'scan this repo', or 'find documents'."
---

# Scribe: Discover - Repository Document Scanner

Scan a repository for documents the user might want to organize. Present a summary and let the user choose what to ingest.

## Output Directory

Notes go into `claude-notes/` in the current project directory. See the dictation skill for directory structure details and the fallback rule (no git repo → `~/.claude/notes/`).

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

Use the two-phase session tracking protocol from `shared/references/session-tracking.md`:

**Phase 1 — Write Session Record:**
- Create `~/.scribe/sessions/` if it doesn't exist.
- Write a session file named `{YYYYMMDDTHHMMSS}-discover-{5-random}.md`.
- Record observed patterns (tagged with `[idea-structure]`, `[priority-signal]`, `[style-pref]`, `[behavior]`), explicit statements, and candidate evidence with canonical slugs.
- Capture preferences inferred from selections and exclusions (what the user tends to include or skip).
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

## Quick Invocation

- `/scribe:discover` - scan current repo
- "scribe, scan this repo" - discover mode
- "scribe, what documents are in this project?" - discover mode
- "find all the notes and docs" - discover mode
