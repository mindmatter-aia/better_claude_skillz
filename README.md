# Shared Skillz — Claude Code Skills Repository

A curated collection of high-quality Claude Code skills for sharing across teams and projects.

## 📚 Available Skills

### [`/optimize-context`](optimize-context/) — Smart Context Exclusion

**Version:** 1.0.0
**Status:** ✅ Production Ready

Intelligently manage which files are loaded during `/prime` context gathering, saving 20-50% of tokens while keeping all files accessible.

**Key Features:**
- Three-layer file exclusion model (Hard Block, Noise Filter, Soft Skip)
- Three operating modes: create, analyze, sync
- Automatic project scanning for large files and low-signal patterns
- 15K-115K token savings per `/prime` run
- Cross-platform support (Linux, macOS, Windows)

**Quick Start:**
```bash
# Install
cp -r optimize-context ~/.claude/skills/

# Use
/optimize-context create
```

[📖 Full Documentation](optimize-context/README.md) | [⚡ Quick Install](optimize-context/INSTALL.md)

---

## 🚀 Installation

### Quick Install (All Skills)

**Linux/macOS:**
```bash
# Clone the repository
git clone https://github.com/mindmatter-aia/shared_skillz.git
cd shared_skillz

# Copy all skills to your Claude Code directory
cp -r */ ~/.claude/skills/
```

**Windows PowerShell:**
```powershell
# Clone the repository
git clone https://github.com/mindmatter-aia/shared_skillz.git
cd shared_skillz

# Copy all skills to your Claude Code directory
Get-ChildItem -Directory | ForEach-Object {
    Copy-Item -Recurse -Force $_.FullName "C:\Users\$env:USERNAME\.claude\skills\$($_.Name)"
}
```

### Install Individual Skills

Each skill has its own installation guide:
- [`/optimize-context` Installation](optimize-context/INSTALL.md)

---

## 📋 Skill Quality Standards

All skills in this repository meet these quality criteria:

✅ **Complete Documentation**
- Comprehensive README with usage examples
- Installation guide (INSTALL.md)
- Changelog tracking versions
- Example files and templates

✅ **Cross-Platform Compatibility**
- Works on Linux, macOS, and Windows
- Tested on multiple environments
- Clear platform-specific instructions when needed

✅ **Zero Dependencies** (when possible)
- Uses native tools and commands
- No third-party packages required
- Self-contained and portable

✅ **Production Ready**
- Tested in real projects
- Clear success criteria
- Error handling and validation
- Graceful degradation

✅ **Well-Structured**
- Clear naming conventions
- Organized file structure
- Versioned releases
- Active maintenance

---

## 🎯 Skill Categories

### Context Management
- **[`/optimize-context`](optimize-context/)** — Smart file exclusion for efficient `/prime` loading

### _More categories coming soon..._
- Code Quality
- Testing & Validation
- Project Setup
- Documentation

---

## 📖 Using Skills

### Basic Usage

After installation, skills are available as slash commands in Claude Code:

```
/optimize-context create
/optimize-context analyze
/optimize-context sync
```

### Integration with Other Skills

Many skills work together:
- Use `/optimize-context` before `/prime` for efficient context loading
- Use `/learn` to extract patterns from your workflow
- Use `/evolve` to cluster patterns into new skills

---

## 🤝 Contributing

Have a skill to share? Here's how to contribute:

### Submission Requirements

1. **Complete documentation** (README.md, INSTALL.md, CHANGELOG.md)
2. **Tested in production** on at least one real project
3. **Cross-platform verified** (or clearly documented platform restrictions)
4. **Example files** included where applicable
5. **Version number** (semantic versioning)

### Submission Process

1. Fork this repository
2. Create a new directory for your skill: `your-skill-name/`
3. Add all required files (see structure below)
4. Test installation and usage
5. Submit a pull request with:
   - Clear description of what the skill does
   - Token savings or performance benefits (if applicable)
   - Platform compatibility notes
   - Any dependencies or requirements

### Required File Structure

```
your-skill-name/
├── README.md           # Full documentation
├── INSTALL.md          # Installation guide
├── SKILL.md            # Skill definition (Claude Code format)
├── CHANGELOG.md        # Version history
├── VERSION             # Current version number
├── example.*           # Example files (if applicable)
└── *.md                # Additional reference docs
```

---

## 📊 Skill Metrics

| Skill | Version | Downloads* | Avg. Token Savings | Platforms |
|-------|---------|-----------|-------------------|-----------|
| `/optimize-context` | 1.0.0 | - | 15K-115K | ✅ ✅ ✅ |

*Download metrics coming soon

---

## 🔄 Updates & Versioning

Each skill follows [Semantic Versioning](https://semver.org/):
- **Major (1.x.x)**: Breaking changes
- **Minor (x.1.x)**: New features, backward compatible
- **Patch (x.x.1)**: Bug fixes

Check individual skill CHANGELOGs for update details.

### Staying Updated

```bash
# Update all skills
cd shared_skillz
git pull
cp -r */ ~/.claude/skills/
```

---

## 📝 License

This repository and all skills are provided as-is for personal and team use.

Individual skills may have their own licenses — check each skill's README.

---

## 🌟 Acknowledgments

**Based on:**
- [Cole Medin's PIV Loop](https://github.com/coleam00) — Prime, Plan, Execute, Validate methodology
- [Affaan Mustafa's Everything Claude Code](https://github.com/affaan-m) — Comprehensive Claude Code tooling

**Maintained by:** [mindmatter-aia](https://github.com/mindmatter-aia)

---

## 📞 Support

- **Issues:** [GitHub Issues](https://github.com/mindmatter-aia/shared_skillz/issues)
- **Discussions:** [GitHub Discussions](https://github.com/mindmatter-aia/shared_skillz/discussions)

---

## 🗺️ Roadmap

### Coming Soon
- [ ] Code quality skills (linting, formatting)
- [ ] Testing utilities (coverage, fixtures)
- [ ] Project scaffolding skills
- [ ] Documentation generators
- [ ] Performance profiling skills

### In Progress
- [x] Context optimization (`/optimize-context`) — ✅ Released v1.0.0

---

**Last Updated:** February 10, 2026
**Skills Count:** 1
**Total Lines of Code:** 991
