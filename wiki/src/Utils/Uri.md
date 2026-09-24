---
type: module
path: "@root/src/Utils/Uri.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.35
depth_status: SHALLOW
coupling: 0
interface_stability: 0.9
tags: [module, shallow]
aliases: [Uri]
---

# Uri

## Purpose

Convert between absolute file paths and the `file://` URIs a language server speaks, and between
`pudu://` resource URIs and their parts.

## Interface

### Signatures

```pudu
export fn fileUri(path: Str) -> Str
export fn pathOfFileUri(uri: Str) -> Option[Str]
export fn resourcePath(uri: Str, scheme: Str) -> Option[Str]   // "pudu://docs/x", "pudu://" → "docs/x"
```

### Linkage

- **Requires:** `Std.Text`.
- **Consumed by:** [[src/Domain/Lsp/Conversation]], [[src/Domain/Lsp/Render]], [[src/App/Resources]].

## Algorithm

`fileUri` percent-encodes space, `%`, `#`, and `?` and prefixes `file://`. `pathOfFileUri`
reverses exactly those escapes. `resourcePath` strips the scheme prefix when present.

## Negative Logic (Prohibited Paths)

- No general URL parser: only the characters the compiler's paths can contain are handled.

## Edge Cases

- `pathOfFileUri("http://x")` is `None`.

## Depth

DEPTH 0.35.

## Grill Log

- **Q:** Full RFC 3986 encoding? **A:** No; the language server compares URIs it was given, so
  round-tripping our own encoding is what matters.

## Referenced by

[[src/Utils/_MOC]]
