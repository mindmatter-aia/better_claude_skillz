# `/challenge` -- Quality Gate for Any Work Product

A rigorous self-critique command that pressure-tests plans, prompts, deliverables, scripts, or any output before considering it final. Based on the philosophy: "Is this the best you can do?"

## Usage

```
/challenge path/to/file.md
/challenge (then paste content)
/challenge -sync path/to/project
/challenge -sync
```

## Features

- Evaluates work across six dimensions: Completeness, Clarity, Logic and Structure, Fit for Purpose, Risk and Blind Spots, and The Bar
- Produces a structured Challenge Report with actionable findings and a verdict (SHIP IT / REWORK / RETHINK)
- Auto-iterates up to 3 cycles on Claude-generated work, applying fixes between rounds
- Supports project-specific Challenge Criteria via `-sync` mode that analyzes your codebase and writes tailored review standards into CLAUDE.md
- Batch sync mode discovers all projects and generates criteria across your workspace
- Final Challenge Report always includes iteration summary, improvements made, and remaining findings

## Installation

Copy to your Claude Code commands directory:

**Linux/macOS:**
```bash
mkdir -p ~/.claude/commands/
cp challenge.md ~/.claude/commands/
```

**Windows PowerShell:**
```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\commands"
Copy-Item challenge.md "$env:USERPROFILE\.claude\commands\"
```

## Related

- Works with any command that produces output (plans, prompts, code, deliverables)
- Pairs well with `/plan` to validate implementation plans before execution

---
**Author:** Nick Martin, [PatriotAgentic LLC](https://github.com/mindmatter-aia)
**License:** MIT
