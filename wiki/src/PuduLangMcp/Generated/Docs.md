---
type: module
path: "@root/src/PuduLangMcp/Generated/Docs.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.1
depth_status: SHALLOW
coupling: 1
interface_stability: 0.95
tags: [module, generated]
aliases: [Corpus, Generated Docs]
---

# PuduLangMcp.Generated.Docs

## Purpose

The Pudu language documentation compiled into the server
([[decisions/ADR-0002-compiled-documentation]]). Written only by [[tools/SyncDocs]].

## Interface

### Signatures

```pudu
export const REVISION: Str                         // pudu-lang revision the text came from: "v0.1.3"
export const CHAPTERS: Array[Chapter.Chapter]      // reading order
```

### Linkage

- **Requires:** [[src/PuduLangMcp/Domain/Docs/Chapter]].
- **Consumed by:** [[src/Main]] (placed in the application context), tests.

## Algorithm

Data only. Generated from the pudu-lang `v0.1.3` tag: 26 guide chapters (including Derives and
Deploying), the site pages, the 0.1.0 to 0.1.3 release notes, and 20 playground examples
(including Derives) — 56 documents.

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

- **Q:** Why generate from the release tag rather than `dev`?
  **A:** The server tells a reader what the released compiler does; `dev` documents work not yet
  shipped. A corpus from a tag matches the compiler a reader installs. _Rejected:_ the `dev` head.

## Referenced by

[[CHANGELOG]] · [[decisions/ADR-0002-compiled-documentation]] · [[handoffs/2026-10-07-pudu-0-1-3]] · [[src/PuduLangMcp/Generated/_MOC]] · [[tools/SyncDocs]]
