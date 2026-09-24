---
type: module
path: "@root/src/Generated/Docs.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.1
depth_status: SHALLOW
coupling: 1
interface_stability: 0.95
tags: [module, generated]
aliases: [Corpus, Generated Docs]
---

# Generated.Docs

## Purpose

The Pudu language documentation compiled into the server
([[decisions/ADR-0002-compiled-documentation]]). Written only by [[tools/SyncDocs]].

## Interface

### Signatures

```pudu
export const REVISION: Str                         // pudu-lang commit the text came from
export const CHAPTERS: Array[Chapter.Chapter]      // reading order
```

### Linkage

- **Requires:** [[src/Domain/Docs/Chapter]].
- **Consumed by:** [[src/Main]] (placed in the application context), tests.

## Algorithm

Data only.

## Negative Logic (Prohibited Paths)

- Never edited by hand; a change here without a matching generator run is drift.

## Edge Cases

- The text round-trips byte for byte against the source Markdown with carriage returns removed;
  verified when the corpus was generated.

## Depth

DEPTH 0.1 (SHALLOW). Data.

## Grill Log

- **Q:** One constant per chapter, or one array? **A:** One array in reading order; the order is
  part of the data. _Rejected:_ 24 named constants and a hand-kept list.

## Referenced by

[[src/Generated/_MOC]] · [[tools/SyncDocs]] · [[decisions/ADR-0002-compiled-documentation]]
