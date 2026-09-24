---
type: module
path: "@root/src/Main.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.05
depth_status: SHALLOW
coupling: 1
interface_stability: 0.95
tags: [module, shallow]
aliases: [Main]
---

# Main

## Purpose

The executable entry: `pudu run src/Main.pudu` and `pudu build src/Main.pudu` start the server.

## Interface

### Signatures

```pudu
fn main() -> Int      // PuduLangMcp.serve()
```

### Linkage

- **Requires:** [[src/PuduLangMcp]].
- **Consumed by:** the `pudu` runtime.

## Algorithm

Answer `PuduLangMcp.serve()` as the exit status.

## Negative Logic (Prohibited Paths)

- No logic here.

## Edge Cases

- None.

## Depth

DEPTH 0.05 (SHALLOW). An entry point.

## Grill Log

- **Q:** Merge with the package root? **A:** No; a library module with a `main` would make every
  importer carry an entry point.

## Referenced by

[[src/_MOC]] · [[seams/Transport]]
