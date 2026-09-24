---
type: module
path: "@root/src/PuduLangMcp/Domain/Lsp/Conversation.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
seam: "[[seams/Toolchain]]"
depth_score: 0.6
depth_status: DEEP
coupling: 2
interface_stability: 0.8
tags: [module, deep]
aliases: [Lsp Conversation]
---

# LSP conversation

## Purpose

Write one complete language-server conversation for one question, and read the answer and the
published diagnostics back out of everything the server wrote
([[decisions/ADR-0003-toolchain-subprocess]]).

## Interface

### Signatures

```pudu
export type Question = { method: Str, line: Int, character: Int, newName: Str, query: Str }   // 1-based
export type Answer = { result: Option[Json.Json], diagnostics: Array[Json.Json], error: Option[Str] }

export fn conversation(rootUri: Str, uri: Str, source: Str, question: &Question) -> Str
export fn answerIn(transcript: Str) -> Answer
export fn frame(message: &Json.Json) -> Str
export fn params(uri: Str, question: &Question) -> Json.Json
```

### Linkage

- **Requires:** `Std.Json`, [[src/PuduLangMcp/Utils/JsonAccess]].
- **Consumed by:** [[src/PuduLangMcp/Services/Lsp]].

## Algorithm

`conversation` frames, in order: `initialize` (id 1, `rootUri`, empty capabilities), `initialized`,
`textDocument/didOpen` (skipped when `source` is empty and the method is `workspace/symbol`), the
question (id 2), `shutdown` (id 3), and `exit`. Each message is `Content-Length: <UTF-8 bytes>` then a
blank line then the JSON body.

`params` converts the 1-based position to 0-based and adds what each method needs:

| Method | Extra parameters |
| --- | --- |
| `textDocument/hover`, `definition`, `signatureHelp`, `prepareRename` | position |
| `textDocument/references` | position, `context.includeDeclaration: true` |
| `textDocument/completion` | position, `context.triggerKind: 1` |
| `textDocument/rename` | position, `newName` |
| `textDocument/codeAction` | a zero-width range at the position, `context.diagnostics: []` |
| `textDocument/documentSymbol` | none |
| `workspace/symbol` | `query` only, no document |

`answerIn` cuts the transcript at each `Content-Length:` header; in each piece that has a blank line
(`Text.find`), the body after it is decoded. The body with id 2 gives `result` (JSON `null` is `Some(Null)`) or, when it carries
`error`, the error's message. Every `textDocument/publishDiagnostics` notification's diagnostics are
collected, the last publication for the document winning.

## Negative Logic (Prohibited Paths)

- No body is trusted to be well formed: an undecodable piece is skipped.
- The server is never asked two questions in one conversation.

## Edge Cases

- A transcript with no id-2 reply answers `result: None`, `error: None` (the server was stopped or
  never answered); the caller reports it.
- Positions below 1 convert to 0 (`Math.max(position - 1, 0)`).

## Depth

DEPTH 0.6 (DEEP).

## Grill Log

- **Q:** Cut on headers or count bytes? **A:** Cut on headers. JSON bodies cannot contain a raw
  line break, so a header never occurs inside a body, and cutting avoids byte arithmetic on text.

## Referenced by

[[src/PuduLangMcp/Domain/Lsp/_MOC]]
