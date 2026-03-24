# Installation

## Quick Install

```bash
cp -r prompt-audit ~/.claude/skills/
```

## Manual Install

1. Copy the `prompt-audit/` directory to `~/.claude/skills/`:
   ```bash
   mkdir -p ~/.claude/skills/prompt-audit/references
   cp SKILL.md ~/.claude/skills/prompt-audit/
   cp references/framework.md ~/.claude/skills/prompt-audit/references/
   ```

2. Verify the skill is detected — start a new Claude Code session and type `/prompt-audit`. It should appear in the slash command list.

## File Structure

```
~/.claude/skills/prompt-audit/
├── SKILL.md                     # Slash command definition
└── references/
    └── framework.md             # R/T/S/C/E/N framework specification
```

## Requirements

- Claude Code CLI
- No external dependencies

## Verify Installation

Run in any project directory:

```
/prompt-audit
```

You should see a prompt inventory scan begin. If the project has no LLM prompts, you'll get a zero-results message with suggestions.

## Uninstall

```bash
rm -rf ~/.claude/skills/prompt-audit
```
