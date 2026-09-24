---
type: module
path: "@root/src/Domain/Catalog/Tools.pudu"
fidelity: Active
domain: "[[domain/Tool]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.3
depth_status: SHALLOW
coupling: 1
interface_stability: 0.7
tags: [module, shallow]
aliases: [Tool Catalogue]
---

# Tools (catalogue)

## Purpose

The list of every tool this server offers, in the fixed order `tools/list` answers.

## Interface

### Signatures

```pudu
export fn all() -> Array[ToolSpec.ToolSpec]
export fn find(name: Str) -> Option[ToolSpec.ToolSpec]
```

| Tool | Arguments | Annotations |
| --- | --- | --- |
| `pudu_docs_search` | `query`, `group?`, `limit?` | read-only, idempotent, closed |
| `pudu_docs_read` | `slug`, `group?`, `section?` | read-only, idempotent, closed |
| `pudu_reference_search` | `query`, `limit?` | read-only, idempotent, closed |
| `pudu_module_reference` | `module` | read-only, idempotent, closed |
| `pudu_check` | `source` or `path` | read-only, idempotent, closed |
| `pudu_format` | `source` or `path` | read-only, idempotent, closed |
| `pudu_lint` | `source` or `path` | read-only, idempotent, closed |
| `pudu_run` | `source`, `timeoutMs?` | read-only, not idempotent, closed |
| `pudu_test` | `path?`, `timeoutMs?` | not read-only, not destructive, not idempotent, open |
| `pudu_hover` | `source` or `path`, `line`, `character` | read-only, idempotent, closed |
| `pudu_definition` | same | same |
| `pudu_references` | same | same |
| `pudu_completion` | same | same |
| `pudu_signature_help` | same | same |
| `pudu_code_actions` | same | same |
| `pudu_rename_preview` | same plus `newName` | read-only (edits are returned, not applied) |
| `pudu_document_symbols` | `source` or `path` | read-only, idempotent, closed |
| `pudu_workspace_symbols` | `query` | read-only, idempotent, closed |
| `pudu_toolchain` | none | read-only, idempotent, closed |

Positions are 1-based lines and characters, as an editor shows them.

### Linkage

- **Requires:** [[src/Domain/Catalog/ToolSpec]], [[src/Constants/Server]].
- **Consumed by:** [[src/App/Tools/Registry]].

## Algorithm

Data; `find` is a linear lookup by name.

## Negative Logic (Prohibited Paths)

- No tool name appears twice.

## Edge Cases

- `pudu_run` is read-only because it always runs confined: no files, processes, network, or
  foreign code.
- `pudu_test` runs the project's own suites, which may do anything a test does, so it is neither
  read-only nor closed-world.

## Depth

DEPTH 0.3 (SHALLOW). Data.

## Grill Log

- **Q:** 1-based or 0-based positions? **A:** 1-based: models read line numbers off listings and
  diagnostics, which are 1-based; the conversion to the language server's 0-based positions is one
  place ([[src/Domain/Lsp/Conversation]]).
- **Q:** One `pudu_lsp` tool with a method argument, or one tool per question? **A:** One per
  question; a model chooses among named tools more reliably than among enum values.

## Referenced by

[[src/Domain/Catalog/_MOC]]
