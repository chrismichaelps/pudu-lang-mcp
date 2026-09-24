---
type: module
path: "@root/src/PuduLangMcp/Services/Toolchain.pudu"
fidelity: Active
domain: "[[domain/Toolchain]]"
grammar: "[[grammar/pudu]]"
seam: "[[seams/Toolchain]]"
depth_score: 0.65
depth_status: DEEP
coupling: 3
interface_stability: 0.85
tags: [module, deep, seam, backbone]
aliases: [Toolchain]
---

# Toolchain

## Purpose

The [[seams/Toolchain]] record: where the installed compiler and its standard library are, which
version it is, and the one function that runs it. Every compiler answer in the server goes through
`run`, so tests replace the record and nothing else.

## Interface

### Signatures

```pudu
export type Toolchain = {
  compiler: Option[Str],
  library: Option[Str],
  version: Str,
  run: fn(Array[Str], Str, Str, Int, Int) -> Result[Bounded.Finished, Str]   // arguments, input, directory, deadline, output cap
}

export fn locate() -> Toolchain
export fn scripted(version: Str, library: Option[Str], answer: fn(Array[Str], Str, Str) -> Bounded.Finished) -> Toolchain   // arguments, input, directory
export fn finished(status: Int, output: Str) -> Bounded.Finished
export fn libraryFrom(compiler: Str) -> Option[Str]
```

### Linkage

- **Requires:** [[src/PuduLangMcp/Services/Process/Bounded]], [[src/PuduLangMcp/Constants/Server]], `Std.Env`, `Std.Fs`, `Std.Io`, `Std.Path`.
- **Consumed by:** [[src/Main]], [[src/PuduLangMcp/Services/Lsp]], [[src/PuduLangMcp/Services/Reference]], `App/Tools/*`.

## Algorithm

`locate`:
1. Compiler: `PUDU_BIN` when set and an existing file; otherwise the first `<dir>/pudu` that exists
   along the search path. Canonicalized, so a symbolic link leads to the real installation.
2. Version: `pudu version` within the command deadline, with the leading `pudu ` removed; empty
   when it does not answer.
3. Library: `PUDU_LIB` when it holds `Std/`; otherwise `libraryFrom(compiler)`.
4. `run` calls [[src/PuduLangMcp/Services/Process/Bounded]] with the compiler and the caller's output cap; with no
   compiler it answers `Err("pudu was not found")`.

`libraryFrom` walks up to sixteen ancestors of the compiler's directory and answers the first of
`<a>/lib/pudu`, `<a>/lib`, or `<a>/packages/pudu/<newest series>/lib` that contains `Std/`.

`scripted` builds a record whose `run` answers `answer(arguments, input, directory)` without a
process; the deadline and cap are not consulted.

## Negative Logic (Prohibited Paths)

- Nothing outside this module starts the compiler.
- A missing compiler never stops the server.

## Edge Cases

- Scripted finished runs are never timed out or truncated.
- `PUDU_BIN` naming a missing file falls back to the search path.

## Depth

DEPTH 0.65 (DEEP). A four-field record hides discovery, versioning, and process bounds.

## Grill Log

- **Q:** Where is the library when the compiler is a development build? **A:** Its checkout's
  `packages/pudu/<series>/lib`, found by walking up from the executable, the same place the
  compiler itself looks.

## Referenced by

[[src/PuduLangMcp/Services/_MOC]] · [[seams/Toolchain]] · [[domain/Toolchain]]
