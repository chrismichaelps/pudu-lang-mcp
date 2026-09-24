---
type: module
path: "@root/src/PuduLangMcp/App/Tools/Language.pudu"
fidelity: Active
domain: "[[domain/Tool]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.55
depth_status: MODERATE
coupling: 6
interface_stability: 0.8
tags: [module]
aliases: [Language Tools]
---

# Language tools

## Purpose

The language-server tools: hover, definition, references, completion, signature help, code
actions, rename preview, document symbols, and workspace symbols.

## Interface

### Signatures

```pudu
export fn hover(context: &Context.Context, args: &Arguments.Args) -> Result[Str, ToolError.ToolFailure]
export fn definition(...) · references(...) · completion(...) · signatureHelp(...)
export fn codeActions(...) · renamePreview(...) · documentSymbols(...) · workspaceSymbols(...)
```

### Linkage

- **Requires:** [[src/PuduLangMcp/App/Context]], [[src/PuduLangMcp/Services/Lsp]], [[src/PuduLangMcp/Services/Workspace]], [[src/PuduLangMcp/Domain/Lsp/Conversation]], [[src/PuduLangMcp/Domain/Lsp/Render]], [[src/PuduLangMcp/Domain/Catalog/Arguments]], [[src/PuduLangMcp/Utils/Uri]], [[src/PuduLangMcp/Utils/TextBounds]].
- **Consumed by:** [[src/PuduLangMcp/App/Tools/Registry]].

## Algorithm

`ask` places the code ([[src/PuduLangMcp/Services/Workspace]]), builds the question from `line`, `character`,
and `newName`, and asks [[src/PuduLangMcp/Services/Lsp]] with the file's URI and the target directory as the
root. A language-server error reply answers "The language server answered: <message>". Each tool
renders the result with [[src/PuduLangMcp/Domain/Lsp/Render]]; paths are shown by `shownPath`:

- the inline source file is `<source>` (a scratch directory holds nothing else);
- workspace symbol results have no scratch file;
- workspace files are relative to the workspace;
- standard-library files are `Std/...` relative to the library root;
- anything else is its path.

`definition` adds, under each location, up to `EXCERPT_LINES` lines of the declaration's source
starting at its line, read from the inline source, the workspace, or the library only; a line past
the file's end shows no source.

`workspaceSymbols` asks with no open document, rooted at the workspace.

## Negative Logic (Prohibited Paths)

- Never reads a file outside the workspace, the library, or the scratch directory.
- `renamePreview` never applies its edits.

## Edge Cases

- A location with no readable source shows the location alone.
- A location at or past the line count has no excerpt; a missing library never permits reading
  outside the workspace.

## Depth

DEPTH 0.55.

## Grill Log

- **Q:** How is a workspace-wide result distinguished from inline source? **A:** Pass an
  optional scratch file to path rendering. `None` cannot label an unrelated path as inline source.
  _Rejected:_ a dummy target with a boolean that has no observable effect.
- **Q:** Why show the declaration's source with a definition? **A:** A model asked "where is this
  defined" wants to read it next; answering both saves a round trip, as the LSP bridges for other
  languages do.

## Referenced by

[[src/PuduLangMcp/App/Tools/_MOC]]
