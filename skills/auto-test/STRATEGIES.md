# Test Generation Strategies

Write tests that a human would write. No mechanical "call every function with null" dumps.

## Picking What to Test

1. **Coverage gaps first** — per-file coverage report shows where to look.
2. **Public API surface** — exported functions, class methods, route handlers.
3. **Error paths** — catch blocks, error returns, validation rejections.
4. **Branch points** — if/else, switch, ternary, short-circuit evaluations.
5. **Edge cases in logic** — empty inputs, boundary values, off-by-one scenarios.

## Writing Better Tests

### Happy Path
- Call the function with typical, valid input
- Assert the exact expected output
- Cover the most common code path first

### Error Handling
- Invalid input → assert the right error type/message
- Missing required fields → assert rejection
- Out-of-range values → assert bounds enforcement

### State Transitions
- For stateful code, test the sequence: initial → action → expected state
- Test idempotency where relevant (calling twice = same result)

### Async Code
- Test both resolution and rejection
- Test timeout behavior if applicable
- Test concurrent calls if the code should handle them

### Data Transformations
- Test with empty input, single item, many items
- Test with edge-case values in the data
- Assert structure AND content of output

## Mocking Guidelines

- **Mock what crosses boundaries**: network, filesystem, database, time, randomness
- **Don't mock what you're testing**: if testing a service, mock its dependencies, not itself
- **Use project patterns**: if the codebase uses dependency injection, use that. If it uses jest.mock, use that.
- **Minimal mocks**: mock only what's needed for the test. Over-mocking hides bugs.

## When Stuck

1. **Review results.tsv** — which files yielded good tests? What patterns worked?
2. **Read the source more carefully** — missed branches, hidden error paths, complex conditionals.
3. **Check per-file coverage** — some files may be at 95% and not worth pursuing. Move on.
4. **Try integration-style tests** — test two modules working together instead of one in isolation.
5. **Test the sad path** — if happy paths are covered, error handling often isn't.
6. **Look for untested utilities** — helper functions, formatters, parsers are often skipped.
7. **Move to a different file** — diminishing returns on a well-tested file. Switch targets.
8. **Skip genuinely untestable code** — some code (CLI entry points, framework glue) isn't worth unit testing. Log as "skipped" and move on.
