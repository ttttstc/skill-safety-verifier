# Skill Safety Verifier

<p align="center">
  <img src="https://img.shields.io/badge/Risk-Assessment-green?style=for-the-badge" alt="Risk Assessment">
  <img src="https://img.shields.io/badge/OpenClaw-Skills-orange?style=for-the-badge" alt="OpenClaw Skills">
  <img src="https://img.shields.io/badge/License-MIT-blue?style=for-the-badge" alt="License">
</p>

> Security-first skill vetting for AI agents. Use before installing any skill from ClawdHub, GitHub, or other sources.

## Why This Matters

AI agents run with elevated permissions and can:
- Execute arbitrary commands
- Access file systems  
- Make network requests
- Read environment variables

**Unvetted skills = security risk**

## Features

- 🔍 **Socket/Network Analysis** - Detect suspicious outbound connections
- 🐛 **Vulnerability Scanning** - Check dependencies for known CVEs (GitHub Advisory API)
- 🚩 **Red Flag Detection** - Identify dangerous code patterns
- 📊 **Risk Classification** - Classify skills as Safe/Low/Medium/High
- 📋 **Standardized Reports** - Clear risk assessments with recommendations
- ⚡ **Async Fetch** - Non-blocking vulnerability checks with local cache

## Quick Start

### Installation

```bash
# Via ClawdHub
npx skills add nibazhu/skill-safety-verifier

# Or clone directly
git clone https://github.com/nibazhu/skill-safety-verifier.git
```

### Usage

```bash
# Check a skill before installation
skill-safety-check --source github.com/user/skill-repo

# Verify an already installed skill  
skill-safety-check --verify --skill-name <skill-name>
```

## Risk Classification

| Level | Score | Color | Action |
|-------|-------|-------|--------|
| Safe | 0-10 | 🟢 Green | Install & use freely |
| Low | 11-30 | 🟡 Yellow | Install with caution |
| Medium | 31-60 | 🟠 Orange | Review code first |
| High | 61-100 | 🔴 Red | Do not install |

## Assessment Criteria

### Socket/Network Risk

| Alert Count | Score |
|-------------|-------|
| 0 | 0 |
| 1-2 | 10 |
| 3-5 | 25 |
| >5 | 50 |

### Code Vulnerability Risk

| Severity | Score |
|----------|-------|
| Critical | 25 |
| High | 15 |
| Medium | 10 |
| Low | 5 |

### Permission Scope Risk

| Scope | Score |
|-------|-------|
| Read-only | 0 |
| Network calls | 10 |
| File write | 15 |
| Command execution | 25 |
| Full system access | 50 |

## Red Flags

### Network
- Suspicious domains (non-standard TLDs)
- Hardcoded IP addresses
- DNS lookups to unknown domains
- Excessive outbound connections

### Execution
- `os.system()` / `subprocess` without sanitization
- `eval()` / `exec()` usage
- Shell injection patterns
- Download and execute code

### Data
- Reading sensitive environment variables
- Exfiltrating files
- Logging keystrokes
- Credential harvesting

## Output Examples

### Safe Skill ✅

```
✅ skill-name - Risk Assessment

┌─────────────────────────────────────────┐
│  📊 Risk Radar                          │
├─────────────────────────────────────────┤
│  Network      [░░░░░░░░░░░]  0/50  🟢 │
│  Vulnerabil. [░░░░░░░░░░░░]  0/25  🟢 │
│  Permissions  [░░░░░░░░░░░░]  0/50  🟢 │
│  ─────────────────────────────────────  │
│  TOTAL        [░░░░░░░░░░░░]  0/100 🟢 │
├─────────────────────────────────────────┤
│  Recommendation: ✅ Safe to install     │
└─────────────────────────────────────────┘
```

### Medium Risk Skill ⚠️

```
⚠️ skill-name - Risk Assessment

┌─────────────────────────────────────────┐
│  📊 Risk Radar                          │
├─────────────────────────────────────────┤
│  Network      [████░░░░░░░] 20/50 🟡 │
│  Vulnerabil. [██░░░░░░░░░░]  5/25 🟢 │
│  Permissions  [█████░░░░░░░] 25/50 🟠 │
│  ─────────────────────────────────────  │
│  TOTAL        [████░░░░░░░] 50/100 🟠 │
├─────────────────────────────────────────┤
│  ⚠️ Warnings: Network calls detected   │
│  Recommendation: Review before install  │
└─────────────────────────────────────────┘
```

### High Risk Skill 🚫

```
🚫 skill-name - Risk Assessment

┌─────────────────────────────────────────┐
│  📊 Risk Radar                          │
├─────────────────────────────────────────┤
│  Network      [██████████] 50/50 🔴 │
│  Vulnerabil. [████████████] 25/25 🔴 │
│  Permissions  [██████████] 50/50 🔴 │
│  ─────────────────────────────────────  │
│  TOTAL        [██████████]100/100 🔴 │
├─────────────────────────────────────────┤
│  🚨 DO NOT INSTALL                     │
│  Reasons:                               │
│  1. Multiple critical vulnerabilities   │
│  2. Executes shell commands             │
│  3. No input sanitization               │
└─────────────────────────────────────────┘
```

## Integration

### With ClawdHub

```bash
# Install with auto-verification
npx skills add <owner/repo> --verify
```

### With OpenClaw

The verifier runs automatically before skill installation when enabled in config:

```yaml
# .openclaw/config.yaml
skill_safety:
  enabled: true
  auto_check: true
  block_high_risk: false
```

## Best Practices

1. **Always verify** - Never install unvetted skills
2. **Read the code** - Automated checks aren't enough
3. **Least privilege** - Only grant necessary permissions
4. **Isolate** - Run high-risk skills in containers
5. **Monitor** - Log all skill activity

## Related

- [ClawdHub](https://clawhub.com) - Skill marketplace
- [OpenClaw Docs](https://docs.openclaw.ai) - Platform documentation
- [Skill Writing Guide](https://docs.openclaw.ai/skills/write) - How to write safe skills

## License

MIT License - see [LICENSE](LICENSE) file for details.

---

<p align="center">
  Made with 🦞 by OpenClaw
</p>
