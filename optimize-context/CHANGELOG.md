# Changelog

All notable changes to the `/optimize-context` skill will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [1.0.0] - 2026-02-10

### Added
- Initial release of `/optimize-context` skill
- Three operating modes:
  - `create` - Generate new `.claudeignore` file
  - `analyze` - Preview candidates without writing files
  - `sync` - Update existing `.claudeignore` preserving user patterns
- Automatic project scanning:
  - Large files detection (>2000 lines)
  - Dense directories detection (>50 files)
  - Known low-signal pattern matching
  - Token savings estimation
- Reference library (`default-patterns.md`) with common patterns by project type:
  - Universal patterns (archives, large docs, generated code)
  - Node.js/TypeScript patterns (lock files, minified assets)
  - Python patterns (migrations, bytecode)
  - Docker/Infrastructure patterns (n8n workflows, data directories)
  - Database patterns (seed data, dumps, migrations)
  - Monorepo patterns (test fixtures, build configs)
- `.claudeignore` file format:
  - Gitignore-style syntax
  - Auto-generated patterns tagged with `# [auto]`
  - User patterns preserved during sync
- Integration with `/prime` skill:
  - Pre-context-loading `.claudeignore` check
  - Pattern matching during file discovery
  - Skipped files reporting in output
  - Graceful degradation when no `.claudeignore` exists
- Documentation:
  - Comprehensive README.md
  - Example `.claudeignore` file
  - Installation instructions
  - Usage examples
  - Troubleshooting guide
  - FAQ

### Features
- Smart filtering: never duplicates `.gitignore` patterns
- Soft exclusion: files remain fully accessible, just not loaded by `/prime`
- Token savings: 15K-115K tokens per `/prime` run (20-50% reduction)
- Project type detection: automatically identifies Node.js, Python, Docker, Database projects
- Estimated token savings per pattern category
- User confirmation before writing files

### Technical Details
- Skill type: Instruction-based (no executable scripts)
- Dependencies: None (uses native git commands)
- Platform: Cross-platform (Linux, macOS, Windows)
- Integration: Requires updated `/prime` skill

## [Unreleased]

### Planned Features
- Auto-tagging with file counts and sizes
- Integration with `/learn` for session-informed patterns
- Support for project-specific pattern templates
- `.claudeignore` validation command
- Dry-run mode for `sync`
- Pattern impact analytics (actual token savings tracking)

---

## Version History

**v1.0.0** - Initial public release
- Core functionality complete
- Tested on AI Concierge project (~115K token savings)
- Documentation complete
