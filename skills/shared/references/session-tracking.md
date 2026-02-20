# Session Tracking Protocol

Session tracking enables evidence-based profile learning across Scribe sessions. Each session writes a record file to `~/.scribe/sessions/`; skills read across session files to count evidence before promoting candidate patterns.

## Session File Naming

```
{ISO-timestamp}-{skill}-{5-random}.md
```

- **ISO-timestamp:** `YYYYMMDDTHHMMSS` (colons removed for filesystem safety)
- **skill:** the skill name (`dictation`, `ingest`, `discover`)
- **5-random:** five random alphanumeric characters to prevent collisions

Example: `20260219T143022-dictation-a7k2m.md`

Session files are **write-once, never modified**. This guarantees zero conflict risk from concurrent sessions.

## Session Record Template

```markdown
# Session Record

**Skill:** {skill}
**Timestamp:** {ISO-8601 with timezone}
**Project:** {project directory or "global"}

## Observed Patterns

- [idea-structure] Description of observed pattern
- [priority-signal] Description of observed pattern
- [style-pref] Description of observed pattern
- [behavior] Description of observed pattern

## Explicit Statements

- "User said X" — verbatim or near-verbatim quotes that indicate preferences

## Candidate Evidence

| Slug | Pattern | Source |
|------|---------|--------|
| multi-model-workflow | Uses Claude + Codex concurrently | observed |
| prefers-tables | Chose table layout over bullets | inferred |

## Profile Actions Taken

- Promoted `slug-name` to Style Preferences (3 sessions of evidence)
- Added candidate `new-slug` (first seen)
- Updated Sessions Analyzed count

## Summary

One-line description of what happened in this session.
```

### Pattern Type Tags

Use these tags in the Observed Patterns section:

| Tag | Meaning |
|-----|---------|
| `[idea-structure]` | How the user structures or presents ideas |
| `[priority-signal]` | Signals indicating priority or importance |
| `[style-pref]` | Formatting, verbosity, or output style preferences |
| `[behavior]` | Recurring workflow or tool-use behaviors |

## Canonical Slugs

Each candidate pattern gets a **kebab-case slug** that identifies it across sessions. Slugs must be:

- Lowercase kebab-case: `prefers-concise-output`
- Stable once assigned — never rename a slug for an existing pattern
- Unique within the candidate table

Slugs enable grep-based evidence counting across session files.

## Evidence Counting Algorithm

When updating the profile at session end:

1. **Write the session file first** to `~/.scribe/sessions/`. This captures evidence even if the profile update fails.
2. **Read the current profile** from `~/.scribe/user-profile.md`.
3. **For each observed pattern**, determine its slug and check evidence:
   - Glob `~/.scribe/sessions/*.md` and grep for the slug across all session files.
   - Count the number of distinct session files containing that slug.
4. **Apply promotion rules** (see below).
5. **Write the updated profile** back to `~/.scribe/user-profile.md`.

## Promotion Rules

| Condition | Action |
|-----------|--------|
| User explicitly states a preference in this session | Promote immediately to the appropriate main section |
| Slug appears in 2+ session files | Promote from Candidate Patterns to the appropriate main section |
| Slug appears in <2 session files | Add or update in Candidate Patterns table |

When promoting:

- Add the pattern as a bullet in the appropriate profile section (Idea Structure, Priority Signals, Style Preferences, Common Behaviors).
- Remove the row from the Candidate Patterns table.
- Log the promotion in Profile Evolution Log with date and evidence count.

## Candidate Table Format

The `## Candidate Patterns (Need More Evidence)` section uses a table:

```markdown
## Candidate Patterns (Need More Evidence)

| Slug | Pattern | Sessions | First Seen | Last Seen | Source |
|------|---------|----------|------------|-----------|--------|
| multi-model-workflow | Uses Claude + Codex concurrently | 1 | 2026-02-19 | 2026-02-19 | observed |
| prefers-tables | Chose table layout over bullets | 1 | 2026-02-20 | 2026-02-20 | inferred |
```

- **Sessions:** count of distinct session files containing this slug
- **First Seen / Last Seen:** dates of earliest and most recent session files
- **Source:** `explicit` (user said it), `observed` (seen in behavior), `inferred` (deduced from choices), or `seed-example` (copied from public example)

## v1 to v2 Migration

If a skill reads a profile without `**Profile Format:** v2` near the top:

1. Add `**Profile Format:** v2` after the `Sessions Analyzed` line.
2. Convert any candidate bullet points to table rows:
   - Extract a slug from the bullet text (kebab-case the key phrase).
   - Set Sessions = 1, First Seen = Last Updated date, Last Seen = Last Updated date.
   - Preserve any `[seed-example]` markers as Source = `seed-example`.
3. Add the session directory reference to Runtime Adaptation Notes.

## Reconciliation Pass

Run at session start (Step 0b in each skill):

1. Glob `~/.scribe/sessions/*.md`.
2. Read the current profile's Candidate Patterns table.
3. For each candidate row, grep session files for the slug and recount.
4. If any candidate now has 2+ sessions of evidence, promote it.
5. Update the profile if any changes were made.

This catches promotions that were lost to concurrent write races or interrupted sessions.

## Session Directory

```
~/.scribe/sessions/
|- 20260219T143022-dictation-a7k2m.md
|- 20260219T150115-ingest-b3j9x.md
|- 20260220T091045-discover-c8m2p.md
\- ...
```

## Retention

Keep all session files. If the directory exceeds 100 files, suggest cleanup to the user in the session summary (do not auto-delete).

## Git Commit (Best-Effort)

After writing the session file and updating the profile, attempt to commit both to the `~/.scribe` repo:

```
cd ~/.scribe && git add sessions/ user-profile.md && git commit -m "session: {skill} {date}"
```

If the commit fails (no git repo, hook failure, etc.), continue without blocking the workflow. Session data integrity does not depend on git.
