---
type: module
path: "@root/src/PuduLangMcp/Domain/Catalog/Prompts.pudu"
fidelity: Active
domain: "[[domain/Prompt]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MODERATE
coupling: 1
interface_stability: 0.75
tags: [module]
aliases: [Prompt Catalogue]
---

# Prompts (catalogue)

## Purpose

The prompt templates this server offers and their rendering into messages.

## Interface

### Signatures

```pudu
export type PromptArgument = { name: Str, description: Str, required: Bool } derives Json.Encode
export type PromptSpec = { name: Str, title: Str, description: Str, arguments: Array[PromptArgument] } derives Json.Encode

export const CONVENTIONS: Str
export fn all() -> Array[PromptSpec]
export fn toJson(spec: &PromptSpec) -> Json.Json
export fn render(name: Str, arguments: &Json.Json) -> Result[(Str, Str), RpcError.ProtocolError]   // (description, user text)
```

| Prompt | Arguments |
| --- | --- |
| `pudu_write_code` | `task` (required) |
| `pudu_fix_diagnostics` | `source` (required), `diagnostics` |
| `pudu_review` | `source` (required) |
| `pudu_explain` | `topic` (required) |

### Linkage

- **Requires:** [[src/PuduLangMcp/Errors/RpcError]], [[src/PuduLangMcp/Utils/JsonAccess]].
- **Consumed by:** [[src/PuduLangMcp/App/Prompts]].

## Algorithm

`render` finds the spec (unknown name: `InvalidParams`), requires every required argument as a
non-blank string (`InvalidParams` naming it), and substitutes the arguments into the template.
Every template ends with `CONVENTIONS`: the language rules from [[grammar/pudu]]'s prohibited
patterns and the verification workflow (check, format, lint, test). The rules include derives: the `derives` clause and the traits that can be derived, the
`@json`, `@skip`, and `@default` attributes, writing a strategy with `Std.Meta`, and the
`Meta.collect` trap of [chrismichaelps/pudu-lang#457](https://github.com/chrismichaelps/pudu-lang/issues/457).

`toJson` is the derived `Json.Encode`: the record fields are the wire keys, in declaration order.

## Negative Logic (Prohibited Paths)

- Arguments are inserted as given, inside fenced blocks where they are code; never evaluated.

## Edge Cases

- A non-string argument value is `InvalidParams`; prompt arguments are strings by definition.

## Depth

DEPTH 0.4.

## Grill Log

- **Q:** Should prompts embed documentation text? **A:** No; they name the tools that fetch it, so
  the model reads current text instead of a copy in the prompt.

- **Q:** Why derive the `prompts/list` encoding?
  **A:** The records' fields already are the protocol's keys, so the hand-written object repeated
  the type; the derived encoder writes the same JSON and follows any field added.
  _Rejected:_ the hand-built object (a second list of the same keys).

## Referenced by

[[CHANGELOG]] · [[handoffs/2026-10-07-pudu-0-1-3]] · [[src/PuduLangMcp/Domain/Catalog/_MOC]]
