# AGENTS.md

Guidelines for AI agents working in this repository.

## Repository Overview

This repository contains **Agent Skills** for AI agents following the [Agent Skills specification](https://agentskills.io/specification.md). Skills install to `.agents/skills/` (the cross-agent standard).

- **Name**: Code Quality Skills
- **GitHub**: [tylergibbs1/codeskills](https://github.com/tylergibbs1/codeskills)
- **Creator**: Tyler Gibbs
- **License**: MIT

## Repository Structure

```
codeskills/
├── skills/
│   ├── auto-research/
│   │   ├── SKILL.md        # Main skill instructions
│   │   ├── CONFIG.md        # research.json reference
│   │   ├── CONTEXT.md       # Context window management
│   │   ├── STRATEGIES.md    # Research tactics
│   │   ├── metadata.json
│   │   └── examples/        # Sample configs
│   ├── auto-refactor/
│   │   ├── SKILL.md
│   │   ├── CONFIG.md        # refactor.json reference
│   │   ├── CONTEXT.md
│   │   ├── STRATEGIES.md    # Refactoring tactics
│   │   └── examples/
│   └── fuzz-fix/
│       ├── SKILL.md
│       ├── CONFIG.md         # fuzz.json reference
│       ├── CONTEXT.md
│       ├── STRATEGIES.md     # Input generation tactics
│       └── examples/
├── AGENTS.md
├── LICENSE
└── README.md
```

## Build / Lint / Test Commands

**Skills** are content-only (no build step). Verify manually:
- YAML frontmatter is valid
- `name` field matches directory name exactly
- `name` is 1-64 chars, lowercase alphanumeric and hyphens only
- `description` is 1-1024 characters

## Agent Skills Specification

Skills follow the [Agent Skills spec](https://agentskills.io/specification.md).

### Required Frontmatter

```yaml
---
name: skill-name
description: What this skill does and when to use it. Include trigger phrases.
---
```

### Frontmatter Field Constraints

| Field         | Required | Constraints                                                      |
|---------------|----------|------------------------------------------------------------------|
| `name`        | Yes      | 1-64 chars, lowercase `a-z`, numbers, hyphens. Must match dir.   |
| `description` | Yes      | 1-1024 chars. Describe what it does and when to use it.          |
| `license`     | No       | License name (default: MIT)                                      |
| `metadata`    | No       | Key-value pairs (author, version, etc.)                          |

### Optional Skill Directories

```
skills/skill-name/
├── SKILL.md        # Required - main instructions (<500 lines)
├── references/     # Optional - detailed docs loaded on demand
├── examples/       # Optional - sample configuration files
└── assets/         # Optional - templates, data files
```

## Writing Style Guidelines

### Structure

- Keep `SKILL.md` under 500 lines (move details to supporting files)
- Use H2 (`##`) for main sections, H3 (`###`) for subsections
- Use bullet points and numbered lists liberally
- Short paragraphs (2-4 sentences max)

### Tone

- Direct and instructional
- Professional but approachable
- No excessive emojis

### Clarity Principles

- Clarity over cleverness
- Specific over vague
- Active voice over passive
- One idea per section

## Git Workflow

### Branch Naming

- New skills: `feature/skill-name`
- Improvements: `fix/skill-name-description`
- Documentation: `docs/description`

### Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

- `feat: add skill-name skill`
- `fix: improve clarity in auto-research`
- `docs: update README`

### Pull Request Checklist

- [ ] `name` matches directory name exactly
- [ ] `name` follows naming rules (lowercase, hyphens, no `--`)
- [ ] `description` is 1-1024 chars with trigger phrases
- [ ] `SKILL.md` is under 500 lines
- [ ] No sensitive data or credentials

## Skill Categories

All skills in this repo follow the **autonomous loop pattern**: explore project, configure, loop forever (hypothesize → change → test → measure → keep or revert). When adding new skills, follow this pattern and share the common output format (git branches, results.tsv, run.log).
