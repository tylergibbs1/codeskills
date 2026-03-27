# Input Generation Strategies

Generate inputs that attackers, careless callers, and Murphy's Law would produce.

## By Type

### Strings
- Empty, whitespace-only, very long (10K+)
- Unicode: emoji, RTL, zero-width chars, combining chars
- Null bytes: `"hello\x00world"`
- Injection: SQL, XSS, path traversal, template injection
- Special values: `"null"`, `"undefined"`, `"NaN"`, `"Infinity"`
- Numeric strings: `"1e308"`, `"0x1F"`, `"-0"`

### Numbers
- `0`, `-0`, `NaN`, `Infinity`, `-Infinity`
- Boundaries: `MAX_SAFE_INTEGER`, `MIN_SAFE_INTEGER`, `2**53`
- Floats: `0.1 + 0.2`, `Number.EPSILON`, `1e-308`, `1e308`
- Negative when only positive expected, float when integer expected

### Collections
- Empty, single element, very large (10K+), deeply nested
- Mixed types, sparse arrays, circular references
- Objects: missing required fields, extra fields, wrong types
- Prototype pollution: `{"__proto__": {"isAdmin": true}}`

### Null / Undefined
- `null`, `undefined`, `None` where values expected
- Optional fields omitted vs explicitly null

### Boundaries
- Off-by-one: max-1, max, max+1
- Signed/unsigned: 127/128, 255/256, 2^31-1/2^31
- Array indices: -1, 0, length-1, length, length+1

## When Stuck

1. Review results.tsv — what categories haven't been tried?
2. Read the implementation — look for unchecked assumptions
3. Check error paths — what happens when internal calls fail?
4. Combine inputs — two that individually pass might together fail
5. Fuzz deeper — if A calls B, fuzz B's interface from A's perspective
6. Increase batch size to 50+
7. Move on — some functions are genuinely robust. That's a win.
