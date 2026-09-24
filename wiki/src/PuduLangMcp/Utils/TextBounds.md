---
type: module
path: "@root/src/PuduLangMcp/Utils/TextBounds.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MODERATE
coupling: 0
interface_stability: 0.9
tags: [module]
aliases: [Text Bounds]
---

# TextBounds

## Purpose

Keep text handed to a model within a size, say when it was cut, and pick the part of a passage
around a match.

## Interface

### Signatures

```pudu
export fn bounded(text: Str, limit: Int) -> Str
export fn snippet(text: Str, needle: Str, width: Int) -> Str
export fn singleLine(text: Str) -> Str
```

### Linkage

- **Requires:** `Std.Text`.
- **Consumed by:** [[src/PuduLangMcp/Domain/Docs/Search]], `App/Tools/*`.

## Algorithm

- `bounded`: text of at most `limit` characters is returned unchanged; longer text keeps its first
  `limit` characters and gains a final line `[truncated: N more characters]`. `limit` is not negative.
- `snippet`: find `needle` case-insensitively (an empty needle is no match); take `width`
  characters starting half a width before it, never before the start, prefixed and suffixed with `…`
  where text was dropped. No match answers the first `width` characters, with `…` when more follow.
- `singleLine`: line breaks and tabs become single spaces; runs of spaces collapse.

## Negative Logic (Prohibited Paths)

- `bounded` never answers more than `limit` characters plus its marker line.

## Edge Cases

- A `limit` of 0 answers only the marker, and empty text within it answers empty text.

## Depth

DEPTH 0.4.

## Grill Log

- **Q:** Characters or bytes? **A:** Characters; the cap protects a model's context, which is
  counted in text, and a byte cut could split a character. Byte caps belong to process capture.

## Referenced by

[[src/PuduLangMcp/Utils/_MOC]]
