---
name: optimize-context
description: Scan project for large/low-signal files and generate .claudeignore for smarter /prime loading
argument-hint: "[create|analyze|sync]"
---

# /optimize-context — Smart Context Exclusion

## Purpose

Manage a `.claudeignore` file that tells `/prime` which tracked files to skip during context loading. Files listed in `.claudeignore` remain **fully accessible** — they are not blocked. They are simply not loaded by default during `/prime` or broad exploration.

This is Layer 3 of the exclusion model:

| Layer | Mechanism | Effect |
|-------|-----------|--------|
| Hard Block | `permissions.deny` | Cannot read at all (for secrets) |
| Noise Filter | `.gitignore` | Not tracked, not visible (for junk) |
| **Soft Skip** | **`.claudeignore`** | **Tracked and readable, but skipped by `/prime`** |

## When to Use

- Setting up a new project for the first time
- After noticing `/prime` loaded large files that weren't useful
- After adding large data files, fixtures, or archives to the project
- Periodically (quarterly) to review what's being skipped

## Arguments

`$ARGUMENTS` options:
- **`create`** (default if no argument) — Scan project, generate `.claudeignore`
- **`analyze`** — Scan project and report findings without writing any files
- **`sync`** — Re-scan and update existing `.claudeignore` (preserves user additions)

## Process

### Mode: `create` (Default)

1. **Check for existing `.claudeignore`**
   - If one exists, warn and suggest `sync` mode instead
   - If none exists, proceed

2. **Detect what `.gitignore` already covers**
   - Read `.gitignore` from project root
   - Parse patterns — these are Layer 2, already handled
   - Do NOT duplicate them in `.claudeignore`

3. **Scan project structure for soft-skip candidates**

   Use `git ls-files` to get all tracked files, then identify:

   **Category A: Large individual files (>50KB or >2000 lines)**
   ```bash
   git ls-files | xargs wc -l 2>/dev/null | sort -rn | head -30
   ```
   Flag files >2000 lines that are NOT source code entry points.

   **Category B: Dense directories (>50 tracked files in one dir)**
   ```bash
   git ls-files | sed 's|/[^/]*$||' | sort | uniq -c | sort -rn | head -20
   ```
   Flag directories with high file counts that are data, migrations, or fixtures.

   **Category C: Known low-signal patterns**
   Check for the presence of:
   - `_archive/`, `old/`, `deprecated/` — archived code
   - `migrations/`, `db/migrations/` — migration history (keep latest few)
   - `fixtures/`, `testdata/`, `__fixtures__/` — test data
   - `*.generated.*`, `generated/` — codegen output
   - `*.min.js`, `*.min.css` — minified assets
   - `*.sql` files >100 lines — large SQL dumps
   - `*.json` files >100 lines — large JSON (API specs, fixture data, workflow exports)
   - `n8n/workflows/*.json` — workflow exports (large, machine-readable)
   - `pageindex/data/`, `data/` — indexed/ingested data directories

   **Category D: Session-informed patterns (if available)**
   Review current conversation for files that were:
   - Read but never referenced in subsequent responses
   - Read multiple times with no benefit
   (This is supplementary — project scan is the primary signal.)

4. **Present findings to user**

   Output a categorized report:
   ```
   ## Soft-Skip Candidates

   ### Large Files (tracked, >2000 lines)
   - n8n/workflows/chat_pipeline_v6.json (3,200 lines, ~45K tokens)
   - db/seed-data.sql (1,800 lines, ~25K tokens)

   ### Dense Directories
   - db/migrations/ (47 files, ~15K tokens total)

   ### Archive/Deprecated
   - _archive/ (12 files, ~20K tokens total)

   ### Data/Fixtures
   - pageindex/data/ (8 files, ~30K tokens total)

   **Estimated context savings per /prime run: ~135K tokens**

   These files will remain fully readable. They just won't be loaded
   during /prime or broad exploration by default.
   ```

5. **Ask user for confirmation**
   - Present the proposed `.claudeignore` content
   - Wait for user approval before writing

6. **Write `.claudeignore`**

   Generate the file at project root with clear documentation:
   ```
   # .claudeignore — Soft Context Exclusion
   # These files are NOT blocked. They remain fully accessible.
   # /prime will skip them during context loading to save tokens.
   # Edit freely. Run /optimize-context sync to refresh.
   #
   # NOT listed here (handled by other layers):
   #   - .gitignore patterns (node_modules, dist, .env, etc.)
   #   - permissions.deny in .claude/settings.json (secrets)

   # Large workflow/data exports
   n8n/workflows/*.json

   # Archive and deprecated code
   _archive/

   # Database migrations (historical)
   db/migrations/

   # PageIndex ingested data
   pageindex/data/

   # Large fixture/seed data
   db/seed-data.sql
   ```

7. **Report success**
   ```
   Created .claudeignore with N patterns.
   /prime will now skip these files during context loading.
   Files remain fully accessible — just ask to read any of them.
   Run /optimize-context sync to update after project changes.
   ```

### Mode: `analyze`

Same as steps 2-4 of `create`, but stops after presenting findings. Does NOT write any files. Useful for understanding what `/optimize-context create` would do before committing.

### Mode: `sync`

1. Read existing `.claudeignore`
2. Separate user-added patterns (anything without a `# [auto]` tag) from auto-generated patterns
3. Re-run the project scan (same as `create` steps 2-3)
4. Merge: keep all user patterns, replace auto-generated patterns with fresh scan results
5. Present diff to user
6. Write updated `.claudeignore` after confirmation

Auto-generated lines should be tagged:
```
_archive/  # [auto] archive directory, 12 files
```
User-added lines have no tag and are always preserved.

## `.claudeignore` Format

Gitignore-style syntax:
- `#` for comments
- `directory/` to skip an entire directory
- `*.extension` for file type patterns
- `path/to/specific-file.json` for individual files
- One pattern per line
- Blank lines ignored
- NO negation patterns (keep it simple — if you need a file, just read it)

## Integration with `/prime`

After `.claudeignore` is created, `/prime` will:
1. Read `.claudeignore` at the start of context loading
2. When using `git ls-files`, `find`, or `ls` to discover files, skip paths matching `.claudeignore` patterns
3. When deciding which files to read, skip matches
4. Report skipped categories in the output summary
5. Include a note: "Skipped N files via .claudeignore. Run /optimize-context sync to update."

Files are still readable on demand. If during a task the user says "read the migration files" or "look at the n8n workflow JSON", Claude reads them normally.

## Notes

- `.claudeignore` should be committed to git (it's project configuration, not a secret)
- Add `.claudeignore` to `.gitignore` only if team members don't want shared skip patterns
- The skill lives in dev-env (global), but `.claudeignore` is per-project (local)
- Never put secrets patterns here — use `permissions.deny` for that
- If `.gitignore` already covers a pattern, don't duplicate it in `.claudeignore`
