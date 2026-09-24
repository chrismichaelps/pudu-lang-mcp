---
type: module
path: "@root/src/PuduLangMcp/App/Tools/Docs.pudu"
fidelity: Active
domain: "[[domain/Tool]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.45
depth_status: MODERATE
coupling: 4
interface_stability: 0.8
tags: [module]
aliases: [Docs Tools]
---

# Docs tools

## Purpose

`pudu_docs_search` and `pudu_docs_read` over the compiled corpus.

## Interface

### Signatures

```pudu
export fn search(context: &Context.Context, args: &Arguments.Args) -> Result[Str, ToolError.ToolFailure]
export fn read(context: &Context.Context, args: &Arguments.Args) -> Result[Str, ToolError.ToolFailure]
export const GROUPS: Array[Str]      // docs, pages, releases, examples
```

### Linkage

- **Requires:** [[src/PuduLangMcp/App/Context]], [[src/PuduLangMcp/Domain/Docs/Chapter]], [[src/PuduLangMcp/Domain/Docs/Search]], [[src/PuduLangMcp/Domain/Catalog/Arguments]], [[src/PuduLangMcp/Utils/TextBounds]], [[src/PuduLangMcp/Constants/Server]].
- **Consumed by:** [[src/PuduLangMcp/App/Tools/Registry]], [[src/PuduLangMcp/App/Completion]].

## Algorithm

- `search`: `group`, when given, must be one of `GROUPS` (`OutOfRange`). Rank the sections of that
  group (or all) with [[src/PuduLangMcp/Domain/Docs/Search]] and `limit` (default `DEFAULT_SEARCH_LIMIT`). Each
  hit is two lines: `N. group/slug#anchor — Document › Heading` and a 240-character snippet around
  the first query term. No hit answers a sentence suggesting other words or the reference search.
- `read`: `group` defaults to `docs`. An unknown document is `NotFound`, listing that group's slugs.
  With `section`, the matching section's heading and text; an unknown section is `NotFound`,
  listing the document's anchors. Output is bounded to `DOC_CHARS`.

## Negative Logic (Prohibited Paths)

- No tool here reads a file; the corpus is in memory.

## Edge Cases

- A query of only stop words answers the no-hit sentence.

## Depth

DEPTH 0.45.

## Grill Log

- **Q:** Return whole sections from search? **A:** No; snippets keep a search answer small, and
  the address leads to `pudu_docs_read` for the full text.

## Referenced by

[[src/PuduLangMcp/App/Tools/_MOC]]
