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
export fn chooseLibrary(named: &Array[Option[Str]], compiler: Option[Str]) -> Option[Str]
export fn isBundle(path: Str) -> Bool
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
3. Library: `chooseLibrary` over `PUDU_MCP_LIB` then `PUDU_LIB` — the first that holds `Std/` and
   is not a bundle directory (`isBundle`: its name starts with `pudu-bundle-`); otherwise
   `libraryFrom(compiler)`.
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

- **Q:** Why pass over a `PUDU_LIB` that names a bundle, and read `PUDU_MCP_LIB` first?
  **A:** An executable made by `pudu build` sets `PUDU_LIB` to the directory it unpacks its own
  modules into, overwriting the user's value
  ([chrismichaelps/pudu-lang#460](https://github.com/chrismichaelps/pudu-lang/issues/460)). The
  built server therefore indexed its 19 bundled modules instead of the 214 installed ones, and the
  documented `PUDU_LIB` setting had no effect. `PUDU_MCP_LIB` is a name the runtime leaves alone.
  _Rejected:_ ignoring `PUDU_LIB` entirely (it is still right under `pudu run`).

## Referenced by

[[CHANGELOG]] · [[domain/Toolchain]] · [[handoffs/2026-10-07-pudu-0-1-3]] · [[seams/Toolchain]] · [[src/PuduLangMcp/Services/_MOC]]
