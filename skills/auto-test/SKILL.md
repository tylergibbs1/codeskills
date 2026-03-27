---
name: auto-test
description: >-
  Runs an autonomous test generation loop that finds uncovered code paths, writes
  tests for them, runs the suite, and keeps tests that pass and increase coverage.
  Loops indefinitely. Triggers on "auto-test", "generate tests", "increase coverage",
  "write tests for my codebase", "test generation loop", or "cover untested code".
license: MIT
metadata:
  author: tylergibbs
  version: "1.0.0"
  argument-hint: "[config-file]"
---

# Auto-Test

Autonomous test generation loop: find uncovered code, write a test, run the suite,
keep tests that pass and increase coverage, discard tests that fail or don't help.

**Contract: every new test passes the full suite and increases coverage.
No snapshot spam. No tautological assertions.**

## Discovery Flow

If `testgen.json` exists, skip to [Setup Phase](#setup-phase).

1. **Explore with 2 parallel subagents:**

   **Agent 1 — Structure & Stack**: Directory tree, language/framework, build system,
   project purpose. Identify the test framework (Jest, Vitest, pytest, Go test, etc.)
   and how tests are organized.

   **Agent 2 — Coverage Baseline**: Run existing tests with coverage. Report total
   line/branch coverage, identify the least-covered files, and note any files with
   zero coverage. Find the coverage command and output format.

2. **Present findings and ask one question:**

   > Here's what I found:
   > - [project summary]
   > - [test framework and organization]
   > - [current coverage: X% lines, Y% branches]
   > - [least-covered files]
   >
   > **What should I focus on?**
   > 1. Whole codebase (start with least-covered files)
   > 2. Specific directories or files
   > 3. Specific modules or layers (e.g., "API handlers", "utils")

   Infer config from exploration — see [CONFIG.md](CONFIG.md) for fields.

3. **Write `testgen.json`**, show for confirmation, then proceed.

## Setup Phase

1. Parse `testgen.json`
2. Create branch `autotest/<tag>` (append `-2`, `-3` if exists)
3. Verify existing tests pass and record baseline coverage
4. Add `results.tsv` and `run.log` to `.gitignore`
5. Initialize `results.tsv` with header row
6. Build ranked list of uncovered files/functions from coverage report
7. **This is the last interaction.** From here, fully autonomous.

## The Loop

**LOOP FOREVER. NEVER stop. NEVER ask permission to continue.**

```
1. PICK: Choose the target with lowest coverage from the ranked list.
   Cooldown targets where last 3 test attempts were discarded.
   Review results.tsv for trends.

2. ANALYZE: Read the source file. Understand what the code does,
   its inputs, outputs, side effects, and error paths. Read existing
   tests for the module to understand conventions and test patterns.

3. WRITE TEST: Write one focused test file (or add to existing test file).
   Follow project conventions for:
   - File naming (*.test.ts, test_*.py, *_test.go, etc.)
   - Import style
   - Test structure (describe/it, test functions, etc.)
   - Setup/teardown patterns
   - Mocking patterns already in use

   Target ONE code path per test. Prefer:
   - Happy path first (if untested)
   - Error/edge cases second
   - Branch coverage over line coverage

4. RUN: Execute test suite > run.log 2>&1.
   - New test fails → delete test file, log as "test_failed", move on.
   - Existing tests break → delete test file, log as "broke_existing", move on.
   - All pass → proceed to measure.

5. MEASURE: Run coverage command > run.log 2>&1.
   Extract coverage metric.
   - Coverage increased → keep.
   - Coverage unchanged → discard (test is redundant).
   - Coverage decreased (shouldn't happen if tests pass) → discard.

6. COMMIT (if kept): Add test file only. Descriptive message with
   what's being tested and coverage delta.
   Do NOT commit results.tsv or run.log.

7. LOG: Append to results.tsv (iteration, timestamp, target_file,
   test_file, code_path_tested, coverage_before, coverage_after,
   status, reason).

8. UPDATE RANKING: Re-extract per-file coverage, re-rank targets.

9. Every 10 iterations, print progress summary. GOTO 1.
```

## Test Quality Rules

- **One behavior per test** — each test verifies one code path or one edge case.
- **Descriptive names** — test name should read as a specification:
  `"returns empty array when input is null"` not `"test1"`.
- **Real assertions** — assert specific values, not just "doesn't throw".
  `expect(result).toEqual([1, 2, 3])` not `expect(result).toBeDefined()`.
- **No snapshot abuse** — snapshots are for rendering output, not logic testing.
- **Minimal mocking** — mock external dependencies (network, filesystem, time),
  not internal modules. Follow the project's existing mock patterns.
- **No test interdependence** — each test must pass in isolation.
- **Match project style** — if the project uses factories, use factories.
  If it uses fixtures, use fixtures. Don't introduce new patterns.

## What NOT to Test

- **Private internals** — test through the public interface.
- **Framework code** — don't test that Express routes or React renders.
- **Type system guarantees** — if TypeScript prevents it, don't test for it.
- **Configuration** — don't test that env vars are read correctly.
- **Trivial getters/setters** — no value in testing `getName() { return this.name }`.

## Critical Rules

- **Protect context window** — see [CONTEXT.md](CONTEXT.md). Always redirect output.
- **results.tsv is your memory** — survives git resets, full history.
- **One test per iteration** — isolate variables. If a test fails, you know which one.
- **Never modify source code** — you're writing tests, not fixing bugs. If you find
  a bug, log it in results.tsv as "bug_found" and move on.
- **Follow existing conventions** — read existing tests before writing new ones.
- **When stuck** — see [STRATEGIES.md](STRATEGIES.md).

## Output

- **Git history** on `autotest/<tag>` — each test is a commit with coverage delta
- **results.tsv** — full test generation log
- **run.log** — most recent test/coverage output
