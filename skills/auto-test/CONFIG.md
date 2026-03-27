# testgen.json Reference

## Required Fields

| Field | Description |
|-------|-------------|
| `tag` | Branch name: `autotest/<tag>` |
| `target_files` | Source files to generate tests for (globs OK) |
| `test_dir` | Where to write new test files |
| `test_command` | Must pass for a test to be kept |
| `coverage_command` | Must output coverage data (JSON preferred) |
| `coverage_metric` | How to extract coverage number from output |
| `metric_name` | Human-readable name (e.g., "line_coverage_pct") |

## Optional Fields

| Field | Default | Description |
|-------|---------|-------------|
| `exclude_files` | `[]` | Globs to skip (generated code, vendored, etc.) |
| `test_name_pattern` | auto-detect | Naming convention: `"*.test.ts"`, `"test_*.py"`, etc. |
| `coverage_type` | `"line"` | `"line"` or `"branch"` |
| `min_assertion_count` | `1` | Minimum assertions per test |
| `timeout_seconds` | `300` | Max seconds per test/coverage run |
| `mock_patterns` | `[]` | Modules that should always be mocked (e.g., `["src/db/*"]`) |

## Coverage Command Catalog

| Framework | Coverage Command | Metric Extraction |
|-----------|-----------------|-------------------|
| Jest | `npx jest --coverage --coverageReporters=json-summary` | `jq '.total.lines.pct' coverage/coverage-summary.json` |
| Vitest | `npx vitest run --coverage --reporter=json` | `jq '.total.lines.pct' coverage/coverage-summary.json` |
| pytest | `pytest --cov=src --cov-report=json -q` | `jq '.totals.percent_covered' coverage.json` |
| Go | `go test -coverprofile=cover.out ./...` | `go tool cover -func=cover.out \| tail -1 \| awk '{print $NF}'` |
| c8 (Node) | `npx c8 --reporter=json npm test` | `jq '.total.lines.pct' coverage/coverage-summary.json` |

Default to line coverage if the user has no preference.

## Example

```json
{
  "tag": "cover-api",
  "target_files": ["src/**/*.ts"],
  "exclude_files": ["**/*.test.*", "**/*.spec.*", "**/node_modules/**", "**/dist/**"],
  "test_dir": "src/__tests__",
  "test_name_pattern": "*.test.ts",
  "test_command": "npm test -- --watchAll=false",
  "coverage_command": "npx jest --coverage --coverageReporters=json-summary 2>/dev/null",
  "coverage_metric": "jq '.total.lines.pct' coverage/coverage-summary.json",
  "metric_name": "line_coverage_pct",
  "coverage_type": "line",
  "timeout_seconds": 300,
  "mock_patterns": ["src/db/*", "src/external/*"]
}
```
