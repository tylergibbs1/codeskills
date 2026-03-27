# Context Window Management

You may run for hours generating inputs and fixing edge cases. If test output
fills your context, the loop dies.

## Rules

1. **Always redirect**: `> run.log 2>&1`. Never let test output print to context.
2. **Grep, don't read**: Extract pass/fail results with grep. Never read full logs.
3. **Tail only on crash**: `tail -n 50 run.log` — only for diagnosing unexpected failures.
4. **Read source files as needed**: Re-read functions before editing, but don't read files you aren't modifying.
5. **results.tsv is your memory**: Full fuzzing history. Re-read when reviewing past iterations.
6. **Short commit messages**: One line summary, edge-case details in body.
7. **Batch inputs, not output**: Generate many inputs per iteration, but only log the summary (pass/fail counts), not every individual result.
