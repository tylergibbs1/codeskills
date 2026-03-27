---
name: fuzz-fixing
description: >-
  Generates edge-case inputs for functions, runs them, catches crashes and unexpected
  outputs, applies minimal fixes, and adds regression tests. Loops until no new failures
  are found. Triggers on "fuzz", "fuzz-fix", "harden my code", "find edge cases",
  "crash test", "find bugs with fuzzing", or "harden my API". Good for hardening APIs,
  parsers, validators, and any function that handles untrusted input.
license: MIT
metadata:
  author: tylergibbs
  version: "1.0.0"
  argument-hint: "[config-file]"
---

# Fuzz-Fixing

Autonomous fuzz-and-fix loop: generate adversarial inputs, find crashes, fix the code,
add regression tests, repeat until no new failures.

**Contract: every fix passes the full test suite and includes a regression test.
No silent error suppression.**

## Discovery Flow

If `fuzz.json` exists, skip to [Setup Phase](#setup-phase).

1. **Explore with 2 parallel subagents:**

   **Agent 1 — Structure & Stack**: Directory tree, language/framework, build system,
   project purpose.

   **Agent 2 — Fuzzing Targets**: Exported functions, API endpoints, parsers, validators,
   serializers — anything that accepts external input. Report signatures, types, and
   existing validation.

2. **Present findings and ask one question:**

   > Here's what I found:
   > - [project summary]
   > - [fuzzing targets]
   >
   > **What do you want to fuzz?**
   > e.g., specific functions, an API layer, parsers, or "everything exported"

   Infer config from exploration — see [CONFIG.md](CONFIG.md) for fields and fix styles.

3. **Write `fuzz.json`**, show for confirmation, then proceed.

## Setup Phase

1. Parse `fuzz.json`
2. Create branch `fuzzfix/<tag>` (append `-2`, `-3` if exists)
3. Verify tests pass at baseline
4. Add `results.tsv` and `run.log` to `.gitignore`
5. Initialize `results.tsv` with header row
6. Build inventory of target functions (signatures, param types, return types)
7. **This is the last interaction.** From here, fully autonomous.

## The Loop

**LOOP FOREVER. NEVER stop. NEVER ask permission to continue.**

```
1. PICK: Rotate through target functions. Prefer unfuzzed or least-fuzzed.
   Review results.tsv for trends.

2. GENERATE: Create 5-15 adversarial inputs per batch.
   See STRATEGIES.md for input generation tactics.
   Write a temp test that calls the function with each input,
   wrapping in try/catch to capture crashes and unexpected output.

3. RUN: Execute fuzz test > run.log 2>&1. Parse: which inputs failed?

4. TRIAGE: Classify failures — CRASH, TYPE_ERROR, LOGIC_ERROR, HANG.
   No failures → log as "clean", move on.

5. FIX (one failure at a time):
   - Understand WHY the input causes the failure
   - Apply minimal fix — validation, bounds check, null guard, type check
   - NEVER suppress errors silently. Proper handling only.

6. ADD REGRESSION TEST: Write a test case for the edge-case input.
   Place in test_dir alongside existing tests.

7. VERIFY: Run full test suite > run.log 2>&1.
   Fail → revert fix AND test. Pass → keep.

8. COMMIT: Add fixed source + new test. Descriptive message with
   input, failure type, and fix description.
   Do NOT commit results.tsv or run.log.

9. LOG: Append to results.tsv (iteration, timestamp, function,
   input_summary, failure_type, fix_applied, tests_pass, status).

10. RE-FUZZ same function — the fix may have introduced new edge cases.
    3 consecutive clean runs → advance to next function.

11. Every 10 iterations, print progress summary. GOTO 1.
```

## Critical Rules

- **Protect context window** — see [CONTEXT.md](CONTEXT.md). Always redirect output.
- **results.tsv is your memory** — survives git resets, full history.
- **One fix per commit** — each edge case gets its own fix and regression test.
- **Never suppress errors** — `catch (e) {}` is not a fix.
- **Don't change behavior for valid inputs** — existing test suite enforces this.
- **When stuck** — see [STRATEGIES.md](STRATEGIES.md).

## Output

- **Git history** on `fuzzfix/<tag>` — each fix is a commit with the edge case documented
- **Regression tests** — permanent test cases for every edge case found
- **results.tsv** — full fuzzing log
- **run.log** — most recent test output
