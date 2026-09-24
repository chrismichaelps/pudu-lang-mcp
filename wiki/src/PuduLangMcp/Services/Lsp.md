---
type: module
path: "@root/src/PuduLangMcp/Services/Lsp.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
seam: "[[seams/Toolchain]]"
depth_score: 0.45
depth_status: MODERATE
coupling: 3
interface_stability: 0.85
tags: [module]
aliases: [Lsp Service]
---

# Lsp service

## Purpose

Ask the installed language server one question through the [[seams/Toolchain]] and answer what it
said, or a tool failure saying why it said nothing.

## Interface

### Signatures

```pudu
export fn ask(toolchain: &Toolchain.Toolchain, rootUri: Str, uri: Str, source: Str, directory: Str, question: &Conversation.Question) -> Result[Conversation.Answer, ToolError.ToolFailure]
```

### Linkage

- **Requires:** [[src/PuduLangMcp/Services/Toolchain]], [[src/PuduLangMcp/Domain/Lsp/Conversation]], [[src/PuduLangMcp/Errors/ToolError]], [[src/PuduLangMcp/Constants/Server]].
- **Consumed by:** [[src/PuduLangMcp/App/Tools/Language]].

## Algorithm

1. No compiler: `ToolchainMissing`.
2. `run(["lsp"], conversation(...), directory, LSP_TIMEOUT_MS)`; a start failure is `CommandFailed`.
3. Parse the output with `answerIn`. A result or an error message is answered as is. With neither:
   `TimedOut("pudu lsp", LSP_TIMEOUT_MS)` when the run was stopped, otherwise
   `Unavailable("The language server ended without answering.")`.

## Negative Logic (Prohibited Paths)

- A language-server error reply is an answer (its message is shown), not a tool failure.

## Edge Cases

- A partial transcript from a stopped server still yields its answer when the answer was written.

## Depth

DEPTH 0.45.

## Grill Log

- **Q:** Retry on timeout? **A:** No; the deadline already bounds the model's wait, and a retry
  doubles it for the same input.

## Referenced by

[[src/PuduLangMcp/Services/_MOC]]
