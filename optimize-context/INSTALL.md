# Quick Installation Guide

## Prerequisites

- Claude Code installed
- Access to `~/.claude/skills/` directory (or `C:\Users\<username>\.claude\skills\` on Windows)

## Installation Steps

### Option 1: Manual Copy (Recommended)

**Linux/macOS:**
```bash
# From the skill_share directory
cp -r optimize-context ~/.claude/skills/
```

**Windows PowerShell:**
```powershell
# From the skill_share directory
Copy-Item -Recurse -Force optimize-context C:\Users\$env:USERNAME\.claude\skills\
```

**Windows Command Prompt:**
```cmd
# From the skill_share directory
xcopy /E /I optimize-context C:\Users\%USERNAME%\.claude\skills\optimize-context
```

### Option 2: Symbolic Link (For Development)

**Linux/macOS:**
```bash
ln -s "$(pwd)/optimize-context" ~/.claude/skills/optimize-context
```

**Windows PowerShell (Run as Administrator):**
```powershell
New-Item -ItemType SymbolicLink -Path "C:\Users\$env:USERNAME\.claude\skills\optimize-context" -Target "$PWD\optimize-context"
```

## Post-Installation: Update `/prime` Skill

**CRITICAL:** The `/optimize-context` skill requires an updated `/prime` skill to function.

### 1. Locate Your `/prime` Skill

```bash
# Linux/macOS
open ~/.claude/skills/prime/SKILL.md

# Windows
notepad C:\Users\%USERNAME%\.claude\skills\prime\SKILL.md
```

### 2. Add `.claudeignore` Integration Section

Insert this block **immediately after the frontmatter** (after the closing `---`), before the "Check $ARGUMENTS" line:

```markdown
## .claudeignore Integration

**Before any context loading**, check for a `.claudeignore` file:

1. Look for `.claudeignore` in the project root (same directory as CLAUDE.md or package.json)
2. If found, read it and parse the patterns (skip comment lines starting with `#` and blank lines)
3. During all subsequent file discovery and reading steps:
   - When listing files (git ls-files, find, ls), mentally filter out paths matching `.claudeignore` patterns
   - When deciding which files to read, skip matches
   - Track what was skipped for the output report
4. At the end of the prime output, add:

\`\`\`
## Context Optimization (.claudeignore)
Skipped N files/directories matching .claudeignore patterns:
- [pattern]: [description of what was skipped]
Files remain fully accessible — ask to read any of them directly.
To update: /optimize-context sync
\`\`\`

**Pattern matching rules (simplified gitignore):**
- `directory/` — skip entire directory and all contents
- `*.ext` — skip all files with that extension
- `path/to/file` — skip that specific file
- Patterns are relative to project root

**Important:**
- `.claudeignore` is a SOFT SKIP. It only affects /prime context loading.
- If the user asks to read a skipped file, read it normally.
- Do NOT confuse this with `permissions.deny` (hard block) or `.gitignore` (untracked files).
- If no `.claudeignore` exists, proceed normally (no warning needed).
```

### 3. Update Token Budget Notes

Find the "Token Budget" sections in both **Quick Mode** and **Full Mode** and add this line after the breakdown:

```markdown
**With .claudeignore**: Token usage may be 20-50% lower depending on how many large tracked files are skipped.
```

## Verification

### 1. Check Skill is Available

In Claude Code, type `/optimize-context` and press Tab. It should autocomplete.

### 2. Test in a Project

```bash
cd ~/your-project
```

In Claude Code:
```
/optimize-context analyze
```

You should see a scan of your project with soft-skip candidates.

### 3. Test `/prime` Integration

After creating a `.claudeignore`:
```
/prime
```

The output should include a "Context Optimization" section showing what was skipped.

## Troubleshooting

### Skill not found

**Check installation path:**
```bash
# Should show SKILL.md
ls ~/.claude/skills/optimize-context/SKILL.md
```

### `/prime` not skipping files

**Verify you updated `/prime` SKILL.md** with the `.claudeignore` integration section.

### Pattern not matching

**Check syntax:**
- Use trailing slash for directories: `migrations/`
- Use globs: `*.json`
- Paths relative to project root: `src/data/` not `./src/data/`

## Need Help?

See the full README.md for:
- Detailed usage guide
- Pattern examples by project type
- FAQ
- Best practices

---

**Installation complete!** You can now use `/optimize-context` in your projects.
