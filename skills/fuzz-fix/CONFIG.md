# fuzz.json Reference

## Required Fields

| Field | Description |
|-------|-------------|
| `tag` | Branch name: `fuzzfix/<tag>` |
| `target_files` | Files containing functions to fuzz (globs OK) |
| `test_command` | Must pass after every fix |
| `test_dir` | Where to write regression tests |

## Optional Fields

| Field | Default | Description |
|-------|---------|-------------|
| `target_functions` | all exported | Specific functions to fuzz |
| `exclude_functions` | `[]` | Functions to skip |
| `timeout_seconds` | `30` | Max seconds per fuzz run |
| `max_inputs_per_batch` | `15` | Edge-case inputs per iteration |
| `consecutive_clean_to_advance` | `3` | Clean runs before moving to next function |
| `fix_style` | `"defensive"` | `"defensive"` (reject with errors) or `"permissive"` (coerce gracefully) |

## Fix Styles

**defensive** (default): Reject bad input with clear errors. Best for libraries/public APIs.

**permissive**: Coerce or return null/defaults. Best for UI code/internal utils.

## Example

```json
{
  "tag": "harden-api",
  "target_files": ["src/api/**/*.ts"],
  "target_functions": ["parseRequest", "validatePayload", "formatResponse"],
  "test_command": "npm test -- --watchAll=false",
  "test_dir": "src/api/__tests__",
  "timeout_seconds": 30,
  "max_inputs_per_batch": 10,
  "fix_style": "defensive"
}
```
