# Skill Safety Verifier

**[English](README.md) | [中文](README_zh.md)**

> Security-first skill vetting for AI agents. Use before installing any skill from ClawdHub, GitHub, or other sources.

## Why This Matters

AI agents run with elevated permissions and can:
- Execute arbitrary commands
- Access file systems
- Make network requests
- Read environment variables

**Unvetted skills = security risk**

## Features

- Code Pattern Scanning - Detect dangerous code (exec/eval/subprocess)
- GitHub Advisory API - Check dependencies for known CVEs
- Permission Analysis - Analyze required permissions from config
- Risk Classification - Classify skills as Safe/Low/Medium/High
- Risk Radar Visualization - Progress bar with color-coded scores

---

## Quick Start

### Installation

```bash
# Via ClawdHub
npx skills add ttttstc/skill-safety-verifier

# Or clone directly
git clone https://github.com/ttttstc/skill-safety-verifier.git
```

### Usage

```bash
# Analyze a skill
python analyzer.py /path/to/skill

# Output JSON
python analyzer.py /path/to/skill --json
```

---

## Workflow

```
User triggers installation
    |
    v
1. Clone skill repository
    |
    v
2. Parallel scanning:
   - Scan code patterns (exec/eval/subprocess)
   - Check GitHub Advisory API
   - Analyze permissions
    |
    v
3. Calculate risk scores
    |
    v
4. Render Risk Radar
    |
    v
User decision
```

---

## Risk Classification

| Level | Score | Color | Action |
|-------|-------|-------|--------|
| Safe | 0-10 | Green | Install & use freely |
| Low | 11-30 | Yellow | Install with caution |
| Medium | 31-60 | Orange | Review code first |
| High | 61-100 | Red | Do not install |

---

## Output Examples

### Safe Skill

```
skill-name - Risk Assessment

Risk Radar:
  Network      0/50  Green
  Vulnerabil. 0/25  Green
  Permissions 0/50  Green
  Total       0/100 Green

Recommendation: Safe to install
```

### Medium Risk Skill

```
skill-name - Risk Assessment

Risk Radar:
  Network      20/50 Yellow
  Vulnerabil.  5/25  Green
  Permissions 25/50 Orange
  Total        50/100 Orange

Warning: Network calls detected
Recommendation: Review code before install
```

---

## Integration

### With ClawdHub

```bash
# Install with auto-verification
npx skills add <owner/repo> --verify
```

---

## Best Practices

1. Always verify - Never install unvetted skills
2. Read the code - Automated checks aren't enough
3. Least privilege - Only grant necessary permissions
4. Isolate - Run high-risk skills in containers

---

## Related

- ClawdHub (https://clawhub.com) - Skill marketplace
- GitHub Advisory API (https://api.github.com/advisories)

---

## License

MIT License - see LICENSE file for details.
