# Code Quality Skills for AI Agents

A collection of autonomous AI agent skills for code quality, hardening, and optimization. Built for engineers who want AI coding agents to continuously improve their codebase. Works with Claude Code, OpenAI Codex, Cursor, Windsurf, and any agent that supports the [Agent Skills spec](https://agentskills.io).

Built by [Tyler Gibbs](https://github.com/tylergibbs1).

## What are Skills?

Skills are markdown files that give AI agents specialized knowledge and workflows for specific tasks. These skills run autonomously in infinite loops — you start them and walk away. They modify code, measure results, keep improvements, and discard failures.

## How Skills Work Together

All three skills share a common pattern: autonomous loop with measurable outcomes.

```
┌─────────────────────────────────────────────────────────────────┐
│                    Autonomous Loop Pattern                       │
│                                                                 │
│   Explore project → Configure → Loop forever:                   │
│     hypothesize → change → test → measure → keep or revert     │
│                                                                 │
│   Output: git history + results.tsv + run.log                   │
└────────────────────┬────────────────────┬───────────────────────┘
                     │                    │
    ┌────────────────┼────────────────────┼────────────────┐
    │                │                    │                │
    ▼                ▼                    ▼                │
┌──────────┐  ┌────────────┐  ┌───────────────┐          │
│  auto-   │  │   auto-    │  │   fuzz-fix    │          │
│ research │  │  refactor  │  │               │          │
├──────────┤  ├────────────┤  ├───────────────┤          │
│ Optimize │  │ Improve    │  │ Harden code   │          │
│ any      │  │ code       │  │ against edge  │          │
│ metric   │  │ quality    │  │ cases         │          │
│          │  │ metrics    │  │               │          │
│ ML, perf,│  │ lint, cov, │  │ APIs, parsers,│          │
│ codegen  │  │ types,     │  │ validators    │          │
│          │  │ complexity │  │               │          │
└──────────┘  └────────────┘  └───────────────┘          │
    │                │                    │                │
    └────────────────┴────────────────────┘                │
         Skills share: git branching, results.tsv,         │
         context window management, strategy rotation      │
└─────────────────────────────────────────────────────────┘
```

## Available Skills

| Skill | Description | Config |
|-------|-------------|--------|
| [auto-research](skills/auto-research/) | Autonomous experiment loop — modify code, measure a target metric, keep improvements, discard failures. Works in any project with a measurable metric (ML training, performance benchmarks, compiler optimization). | `research.json` |
| [auto-refactor](skills/auto-refactor/) | Autonomous refactoring loop — pick files, apply behavior-preserving changes, run tests, measure a quality metric (lint warnings, coverage, type errors, complexity), keep improvements or revert. | `refactor.json` |
| [fuzz-fix](skills/fuzz-fix/) | Autonomous fuzz-and-fix loop — generate adversarial edge-case inputs, catch crashes, fix the code, add regression tests. Loops until no new failures are found. | `fuzz.json` |

## Installation

### Option 1: CLI Install (Recommended)

```bash
# Install all skills
npx skills add tylergibbs1/codeskills

# Install specific skills
npx skills add tylergibbs1/codeskills --skill auto-research auto-refactor

# List available skills
npx skills add tylergibbs1/codeskills --list
```

### Option 2: Clone and Copy

```bash
git clone https://github.com/tylergibbs1/codeskills.git
cp -r codeskills/skills/* .agents/skills/
```

### Option 3: Git Submodule

```bash
git submodule add https://github.com/tylergibbs1/codeskills.git .agents/codeskills
```

Then reference skills from `.agents/codeskills/skills/`.

### Option 4: Fork and Customize

1. Fork this repository
2. Customize skills for your specific needs
3. Clone your fork into your projects

## Usage

Once installed, invoke skills directly:

```
/auto-research    → optimize any measurable metric
/auto-refactor    → improve code quality metrics
/fuzz-fix         → harden code against edge cases
```

Or describe what you want:

```
"Optimize my training loop"
→ Uses auto-research

"Clean up lint warnings in my codebase"
→ Uses auto-refactor

"Harden my API handlers against bad input"
→ Uses fuzz-fix
```

Each skill auto-discovers your project, asks what to optimize, writes a config, then runs autonomously. Leave it running and come back to results.

## Skill Details

### auto-research

General-purpose autonomous experiment loop. Works in any project with a measurable metric. Inspired by [Karpathy's autoresearch](https://github.com/karpathy/autoresearch).

- **Use for**: ML training, algorithm tuning, performance benchmarks, compiler optimization
- **Config**: `research.json` — define modifiable files, run command, metric to optimize
- **Output**: Git history on `autoresearch/<tag>`, `results.tsv`, `run.log`

### auto-refactor

Behavior-preserving code quality improvements, measured and verified.

- **Use for**: Lint cleanup, type error reduction, test coverage improvement, complexity reduction
- **Config**: `refactor.json` — define target files, test command, quality metric
- **Output**: Git history on `autorefactor/<tag>`, `results.tsv`, `run.log`

### fuzz-fix

Adversarial input generation with automatic fixes and regression tests.

- **Use for**: Hardening APIs, parsers, validators, any function handling untrusted input
- **Config**: `fuzz.json` — define target files/functions, test command, fix style
- **Output**: Git history on `fuzzfix/<tag>`, regression tests, `results.tsv`, `run.log`

## The Contract

All three skills share the same guarantee:

> Every change either improves the codebase or gets thrown away. Tests must pass. The metric must not regress. No exceptions.

## Contributing

Found a way to improve a skill? Have a new skill to suggest? PRs and issues welcome.

## License

[MIT](LICENSE) — Use these however you want.
