---
type: module
path: "@root/src/PuduLangMcp/Domain/Reference/Derives.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MODERATE
coupling: 1
interface_stability: 0.7
tags: [module]
aliases: [Reference Derives]
---

# Reference derives

## Purpose

Read the derive strategies a module publishes from its source text, because the compiler's
reference output omits them ([chrismichaelps/pudu-lang#458](https://github.com/chrismichaelps/pudu-lang/issues/458)).

## Interface

### Signatures

```pudu
export fn strategiesOf(moduleName: Str, text: Str) -> Array[Entry.Entry]
```

### Linkage

- **Requires:** [[src/PuduLangMcp/Domain/Reference/Entry]], `Std.List`, `Std.Option`, `Std.Text`.
- **Consumed by:** [[src/PuduLangMcp/Services/Reference]].

## Algorithm

- Each line starting `export derive ` is a published strategy. Its signature is the header before
  the body's brace, `derive Show for T: Record`; its name is the trait; its kind is `derive`.
- The `///` lines directly above it, each without the marker and one following space, are its
  documentation. Any other line clears the gathered comment.

## Negative Logic (Prohibited Paths)

- A `derive` without `export`, an indented one, or one inside a line comment is not published.

## Edge Cases

- A trait with strategies for both shapes yields two entries, one per shape.

## Depth

DEPTH 0.4.

## Grill Log

- **Q:** Why scan text when the compiler knows the declarations?
  **A:** Its JSON output does not carry them in 0.1.3; the scan is a stopgap removed once
  pudu-lang#458 is fixed. A published strategy starts at the first column with `export derive`,
  which the standard library and the formatter both hold to. _Rejected:_ leaving derives out of
  the reference (the headline feature of 0.1.3 would be invisible to clients).

## Referenced by

[[CHANGELOG]] · [[grammar/pudu]] · [[handoffs/2026-10-07-pudu-0-1-3]] · [[src/PuduLangMcp/Domain/Reference/_MOC]] · [[src/PuduLangMcp/Services/Reference]]
