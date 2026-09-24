---
type: module
path: "@root/src/PuduLangMcp/Domain/Reference/Entry.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MODERATE
coupling: 2
interface_stability: 0.85
tags: [module]
aliases: [Reference Entry, Api Entry]
---

# Reference entry

## Purpose

One public declaration, as the installed toolchain reports it through `pudu doc --json`, and the
lookups over the whole catalogue: a module's declarations, the module list, and ranked name search.

## Interface

### Signatures

```pudu
export type Entry = { moduleName: Str, kind: Str, name: Str, signature: Str, doc: Str }
export type Found = { entry: Entry, score: Int }

export fn modules(entries: &Array[Entry]) -> Array[Str]                // sorted, distinct
export fn ofModule(entries: &Array[Entry], moduleName: Str) -> Array[Entry] // catalogue order
export fn render(entry: &Entry) -> Str                                  // "kind name :: signature" + doc
export fn renderModule(entries: &Array[Entry], moduleName: Str) -> Str
export fn search(entries: &Array[Entry], query: Str, limit: Int) -> Array[Found]
```

### Linkage

- **Requires:** `Std.List`, `Std.Text`.
- **Consumed by:** [[src/PuduLangMcp/Services/Reference]], [[src/PuduLangMcp/App/Tools/Reference]], [[src/PuduLangMcp/App/Resources]], [[src/PuduLangMcp/App/Completion]].

## Algorithm

`search` lowercases the query and, for each entry, with `name`, `qualified` (`moduleName.name`),
`signature`, and `doc` lowercased:

- exact name or exact qualified name: 100;
- name starts with the query: 60; qualified name contains it: 45; name contains it: 40;
- signature contains it: 25; doc contains it: 10;
- the best single rule counts, plus 5 when the entry is a `fn`.

Positive scores are kept and taken to `limit` after a stable sort by descending score, so equal
scores keep catalogue order. `modules` is the distinct module names in their natural order.

## Negative Logic (Prohibited Paths)

- An empty query answers nothing.

## Edge Cases

- `ofModule` of an unknown module is empty; the caller reports not found.
- Methods implemented for a type carry kinds such as `fn (Str)`; they count as functions.

## Depth

DEPTH 0.5.

## Grill Log

- **Q:** Why not always call `pudu search`? **A:** Name lookups over the compiled catalogue need
  no process and answer instantly; type-shape queries (containing `->`) still go to the toolchain,
  which unifies shapes ([[src/PuduLangMcp/App/Tools/Reference]]).

## Referenced by

[[src/PuduLangMcp/Domain/Reference/_MOC]]
