---
type: module
path: "@root/src/PuduLangMcp/Services/Reference.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
seam: "[[seams/Toolchain]]"
depth_score: 0.6
depth_status: DEEP
coupling: 4
interface_stability: 0.8
tags: [module, deep]
aliases: [Reference Service]
---

# Reference service

## Purpose

Find every module file the workspace can import from outside itself — the standard library and
installed packages — and build the declaration index from the compiler's own reference output
([[decisions/ADR-0006-reference-from-toolchain]]).

## Interface

### Signatures

```pudu
export type ModuleFile = { moduleName: Str, path: Str, origin: Str }   // origin: "Std" or the package directory

export fn moduleFiles(library: Option[Str], workspaceRoot: Str) -> Array[ModuleFile]
export fn fileOf(files: &Array[ModuleFile], moduleName: Str) -> Option[ModuleFile]
export fn build(toolchain: &Toolchain.Toolchain, files: &Array[ModuleFile]) -> Result[Array[Entry.Entry], ToolError.ToolFailure]
export fn moduleEntries(toolchain: &Toolchain.Toolchain, file: &ModuleFile) -> Result[Array[Entry.Entry], ToolError.ToolFailure]
```

### Linkage

- **Requires:** [[src/PuduLangMcp/Services/Toolchain]], [[src/PuduLangMcp/Domain/Reference/Decode]], [[src/PuduLangMcp/Domain/Code/SourceFile]], [[src/PuduLangMcp/Constants/Server]], `Std.Concurrent`, `Std.Fs`, `Std.Io`, `Std.Path`.
- **Consumed by:** [[src/PuduLangMcp/App/Tools/Reference]], [[src/PuduLangMcp/App/Resources]], [[src/PuduLangMcp/App/Completion]].

## Algorithm

- `moduleFiles`: walk `<library>/Std` for `.pudu` files, naming each by its path under the library.
  Then, for each directory under `<workspace>/deps`, walk its `src` (or the directory itself when it
  has no `src`) the same way. Results are ordered by module name; the first of a duplicate name wins (the library before
  packages). Directories are walked at most `MAX_WALK_DEPTH` levels below each root.
- `build`: split the files into chunks of `REFERENCE_CHUNK` and, with at most `REFERENCE_WORKERS` at
  once, run `pudu api --json` then `pudu doc --json` over each chunk (each within
  `REFERENCE_TIMEOUT_MS` and `REFERENCE_OUTPUT_CAP_BYTES`). Decode each chunk ([[src/PuduLangMcp/Domain/Reference/Decode]]) and join the chunks,
  keeping a declaration once. Any chunk failing is a tool failure naming it.
- `moduleEntries`: the same two runs over one file, keeping only that module's declarations.

## Negative Logic (Prohibited Paths)

- The workspace's own `src/` is not indexed: its code changes under the server, and the language
  server's symbol tools answer for it.
- Walking never follows a symbolic link to a directory.

## Edge Cases

- No library and no packages: an empty file list and an empty index.

## Depth

DEPTH 0.6 (DEEP).

## Grill Log

- **Q:** Chunk size? **A:** 25 files, four workers: each run compiles its chunk's imports once, and
  four runs keep a laptop responsive while the first search is being indexed.

## Referenced by

[[src/PuduLangMcp/Services/_MOC]]
