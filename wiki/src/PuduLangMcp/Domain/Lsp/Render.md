---
type: module
path: "@root/src/PuduLangMcp/Domain/Lsp/Render.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.55
depth_status: MODERATE
coupling: 2
interface_stability: 0.8
tags: [module]
aliases: [Lsp Render]
---

# LSP render

## Purpose

Turn language-server results into short text a model reads well: 1-based positions, workspace-
relative paths, and one line per item.

## Interface

### Signatures

```pudu
export fn hover(result: &Json.Json) -> Str
export fn locations(result: &Json.Json, shown: fn(Str) -> Str) -> Array[Location]
export fn locationLine(held: &Location) -> Str               // "path:line:character"
export fn completion(result: &Json.Json, limit: Int) -> Str
export fn signatureHelp(result: &Json.Json) -> Str
export fn codeActions(result: &Json.Json) -> Str
export fn workspaceEdit(result: &Json.Json, shown: fn(Str) -> Str) -> Str
export fn documentSymbols(result: &Json.Json) -> Str
export fn workspaceSymbols(result: &Json.Json, shown: fn(Str) -> Str) -> Str
export fn diagnostics(items: &Array[Json.Json]) -> Str

export type Location = { uri: Str, path: Str, line: Int, character: Int, endLine: Int }   // 1-based
```

### Linkage

- **Requires:** `Std.Json`, [[src/PuduLangMcp/Utils/JsonAccess]].
- **Consumed by:** [[src/PuduLangMcp/App/Tools/Language]].

## Algorithm

- `hover`: `contents` as markup (`value`), a plain string, or a list of either, joined by blank
  lines. `null` answers "No information at this position."
- `locations`: a `Location`, a list of them, or `LocationLink`s (`targetUri`, `targetRange`).
- `completion`: a list or a `CompletionList.items`; each `label — detail`, at most `limit`, then
  a count of the rest.
- `signatureHelp`: the active signature's label, its documentation, and the active parameter's
  label when the parameter label is a string or a `[start, end]` offset pair into the signature.
- `codeActions`: one line per action title, with its kind when present.
- `workspaceEdit`: `changes` (by URI) or `documentChanges` (`textDocument.uri`, `edits`), one line
  per edit: `path:line:character-endLine:endCharacter → newText`.
- `documentSymbols`: hierarchical `DocumentSymbol`s (children indented) or `SymbolInformation`s;
  `kind name — detail (line N)`, kind named from the LSP symbol-kind table.
- `diagnostics`: `line:character severity [code] message`, severity named 1 error … 4 hint.

## Negative Logic (Prohibited Paths)

- No raw JSON is shown to the model when a known shape can be rendered; an unknown shape falls back
  to its encoded JSON rather than being dropped.

## Edge Cases

- An empty list renders as a sentence saying nothing was found.

## Depth

DEPTH 0.55.

## Grill Log

- **Q:** Show absolute paths? **A:** No; `shown` maps a URI to a workspace-relative path, the
  standard library's `Std/...` path, or `<source>` for inline code, so paths are short and do not
  leak the machine's layout.

## Referenced by

[[src/PuduLangMcp/Domain/Lsp/_MOC]]
