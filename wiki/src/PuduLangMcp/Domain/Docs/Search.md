---
type: module
path: "@root/src/PuduLangMcp/Domain/Docs/Search.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: DEEP
coupling: 2
interface_stability: 0.85
tags: [module, deep]
aliases: [Docs Search]
---

# Docs search

## Purpose

Rank documentation sections against a free-text question so the best few can be shown.

## Interface

### Signatures

```pudu
export type Hit = { section: Chapter.Section, score: Int }

export fn terms(query: Str) -> Array[Str]
export fn score(section: &Chapter.Section, query: Str, words: &Array[Str]) -> Int
export fn search(sections: &Array[Chapter.Section], query: Str, limit: Int) -> Array[Hit]
```

### Linkage

- **Requires:** [[src/PuduLangMcp/Domain/Docs/Chapter]], `Std.List`, `Std.Math`, `Std.Text`.
- **Consumed by:** [[src/PuduLangMcp/App/Tools/Docs]].

## Algorithm

- `terms`: lowercase; split on anything that is not a letter, digit, `_`, or `.`; drop empty terms
  and the stop words in a const set; keep each term once, in order.
- `score` per section, with `heading` and `text` lowercased:
  - whole query phrase (lowercased, trimmed, at least 3 characters) in the heading: +40; in the text: +12;
  - per term: in the heading +10; occurrences in the text, at most 5, +2 each;
  - every term present somewhere: +15.
- `search`: score every section, keep positive scores, and take `limit` after a stable sort by
  descending score, so equal scores keep corpus order.

## Negative Logic (Prohibited Paths)

- A query of only stop words answers nothing rather than everything.
- No section scores for a term it does not contain.

## Edge Cases

- A `limit` of 0 answers nothing.
- A term such as `Option.unwrapOr` stays one term, so qualified names match as written.

## Depth

DEPTH 0.6 (DEEP).

## Grill Log

- **Q:** How is the occurrence cap expressed without a redundant boundary branch? **A:** Use
  `Math.min(occurrences, MAX_COUNTED_OCCURRENCES)`. _Rejected:_ comparing `>` and `>=` around an
  equal result; both calculate the same score.
- **Q:** TF-IDF or an inverted index? **A:** Not at this corpus size: 24 chapters score in a
  single pass well under the response budget. _Rejected:_ an index to keep in step with the corpus.

## Referenced by

[[src/PuduLangMcp/Domain/Docs/_MOC]]
