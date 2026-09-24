---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0003 — Reach the compiler as a bounded subprocess

## Context

Checking, formatting, linting, running, testing, and language-server answers all belong to the
installed `pudu`. Re-implementing any of them would drift from the compiler the user runs.

## Decision

Every compiler answer comes from running the installed `pudu` (`PUDU_BIN`, else the first `pudu` on
`PATH`) with a deadline and an output cap. Language-server questions are one complete conversation
per question — initialize, open, ask, shutdown, exit — written to `pudu lsp` at once, and the
transcript is parsed afterwards. The standard library is the installed one: `PUDU_LIB`, else
`<prefix>/lib/pudu` beside the executable, else a checkout's `packages/pudu/v0.1/lib`.

## Consequences

- Answers match the compiler version the user has.
- A slow or hung compiler costs at most the deadline; its partial output is still reported.
- All of this sits behind [[seams/Toolchain]] so the protocol core is tested without processes.

## Rejected

- A long-lived `pudu lsp` child: keeps per-document state across unrelated requests, which the
  stateless protocol forbids relying on, and needs restart logic.

## Referenced by

[[decisions/_MOC]] · [[seams/Toolchain]] · [[decisions/ADR-0002-compiled-documentation]]
