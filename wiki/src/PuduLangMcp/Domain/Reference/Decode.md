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
export fn entriesOf(docJson: Str, exports: &Set[Str], sources: &Map[Str, Str]) -> Result[Array[Entry.Entry], Str]
```

`sources` maps a module name to its file's text.

### Linkage

- **Requires:** [[src/PuduLangMcp/Domain/Reference/Entry]], `Std.Json`, `Std.List`, `Std.Text`.
- **Consumed by:** [[src/PuduLangMcp/Services/Reference]].

## Algorithm

- Both outputs are read into records that `derives Json.Decode`: `ApiOutput{exports: [{module,
  name}]}` and `DocOutput{entries: [{module, name, kind, signature, doc, span}]}`. `module` is a
  keyword, so its field is `moduleName` with `@json("module")`; `shape` and every other key are
  ignored.
- `exportsOf`: the exports as a set of `module + "." + name`.
- `entriesOf` keeps an item when:
  - its qualified name is exported and its kind names no owner; or
  - its kind is a method, `fn (trait X)` or `fn (X)`, and `X` is exported in its module; or
  - it is an implementation of a method an exported trait of its module declares, such as
    `fn (Int) show` for `Std.Show.Show`.
- A kept item's signature is the compiler's, or — for a type or trait, which the compiler reports
  without one — its declaration read from `sources` by its `span`, which counts characters, with
  doc comment lines removed. A type's declaration carries its `derives` clause.
- Documentation lines are joined by line breaks. An item appearing twice (a module imported by two
  files in one run) is kept once.

## Negative Logic (Prohibited Paths)

- A declaration not in the export set, and not a method of an exported trait or type, never enters
  the reference: private helpers stay private.
- A span reaching past its text, negative, or not a pair answers no declaration rather than a
  wrong slice; `Str.slice` clamps, so an unchecked span would answer the wrong text. An empty span
  slices to empty text.

## Edge Cases

- Output that is not JSON, or does not have the reference shape, is `Err` with the reason.
- A module whose text is not in `sources` keeps the compiler's empty signature.

## Depth

DEPTH 0.55.

## Grill Log

- **Q:** Why keep trait and implementation methods the export list does not name?
  **A:** `pudu api` lists a trait but not its methods, so `Std.Meta.FieldAccess.has` and
  `attributeOr` — the calls every derive strategy makes — were invisible. A method belongs to the
  public surface exactly when its trait or type does. _Rejected:_ trusting the export list alone.
- **Q:** Why read declarations from source by span?
  **A:** The compiler reports types and traits without signatures, so a reader saw `type Point`
  but not its fields or `derives` clause; the span locates the declaration exactly.
  _Rejected:_ re-parsing the file (a second parser to keep in step with the compiler).
- **Q:** Why decode through derived records instead of walking the JSON by hand?
  **A:** The records state the output's shape once, and a missing or mistyped key is refused with
  its path. _Rejected:_ member lookups that turn a missing key into an empty string.
- **Q:** Why both commands? **A:** `doc --json` carries signatures and documentation for every
  declaration, private ones included; `api --json` is the authority on what is public.

## Referenced by

[[CHANGELOG]] · [[grammar/pudu]] · [[handoffs/2026-10-07-pudu-0-1-3]] · [[src/PuduLangMcp/Domain/Reference/_MOC]]
