# Context Window Management

You may run for hours generating tests. If test/coverage output fills your context,
the loop dies.

## Rules

1. **Always redirect**: `> run.log 2>&1`. Never let test or coverage output print to context.
2. **Grep, don't read**: Extract coverage numbers with grep/jq. Never read full logs.
3. **Tail only on crash**: `tail -n 50 run.log` — only for diagnosing test failures.
4. **Read source files before writing tests**: Understand the code, but don't re-read files you already analyzed this iteration.
5. **Read existing tests once per target**: Check conventions, then write. Don't re-read every iteration.
6. **results.tsv is your memory**: Full test generation history. Re-read when reviewing past iterations.
7. **Short commit messages**: One line summary with coverage delta. Details in body.
8. **Coverage reports are large**: Extract only the summary number. Never read per-file coverage into context — use jq/grep to pull specific file stats.
