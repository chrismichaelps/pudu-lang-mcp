---
type: module
path: "@root/src/PuduLangMcp/Domain/Reference/Decode.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.55
depth_status: MODERATE
coupling: 1
interface_stability: 0.8
tags: [module]
aliases: [Reference Decode]
---

# Reference decode

## Purpose

Turn the compiler's `pudu doc --json` and `pudu api --json` output for a set of files into the
public [[src/PuduLangMcp/Domain/Reference/Entry]]s those files declare
([[decisions/ADR-0006-reference-from-toolchain]]).

## Interface

### Signatures

```pudu
export fn exportsOf(apiJson: Str) -> Result[Set[Str], Str]                  // "Module.name"
export fn entriesOf(docJson: Str, exports: &Set[Str]) -> Result[Array[Entry.Entry], Str]
```

### Linkage

- **Requires:** [[src/PuduLangMcp/Domain/Reference/Entry]], `Std.Json`.
- **Consumed by:** [[src/PuduLangMcp/Services/Reference]].

## Algorithm

- `exportsOf`: `{"exports": [{"module", "name"}]}` as a set of `module + "." + name`.
- `entriesOf`: every `entries[]` item whose qualified name is exported becomes an `Entry` with its
  `kind`, `signature`, and `doc` lines joined by line breaks. An item appearing twice (a module
  imported by two files in one run) is kept once.

## Negative Logic (Prohibited Paths)

- A declaration not in the export set never enters the reference: private helpers stay private.

## Edge Cases

- Output that is not JSON, or lacks its array, is `Err` with the reason.

## Depth

DEPTH 0.55.

## Grill Log

- **Q:** Why both commands? **A:** `doc --json` carries signatures and documentation for every
  declaration, private ones included; `api --json` is the authority on what is public.

## Referenced by

[[src/PuduLangMcp/Domain/Reference/_MOC]]
