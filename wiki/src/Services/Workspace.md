---
type: module
path: "@root/src/Services/Workspace.pudu"
fidelity: Active
domain: "[[domain/Workspace]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: DEEP
coupling: 2
interface_stability: 0.85
tags: [module, deep, critical]
aliases: [Workspace]
---

# Workspace

## Purpose

Confine path arguments to the workspace, show paths relative to it, and give inline source a
private place to exist for the length of one command ([[decisions/ADR-0004-workspace-confinement]]).

## Interface

### Signatures

```pudu
export type Workspace = { root: Str }
export type Target = { directory: Str, file: Str, source: Str, scratch: Bool }   // absolute paths

export fn fromEnvironment() -> Workspace
export fn at(root: Str) -> Workspace
export fn resolve(workspace: &Workspace, path: Str) -> Result[Str, ToolError.ToolFailure]
export fn relative(workspace: &Workspace, path: Str) -> Str
export fn withTarget[T](workspace: &Workspace, source: Option[Str], path: Option[Str], use: fn(Target) -> T) -> Result[T, ToolError.ToolFailure]
```

### Linkage

- **Requires:** [[src/Domain/Code/SourceFile]], [[src/Errors/ToolError]], [[src/Constants/Server]], `Std.Fs`, `Std.Io`, `Std.Env`, `Std.Path`.
- **Consumed by:** [[src/Main]], `App/Tools/*`.

## Algorithm

- `fromEnvironment`: `PUDU_MCP_ROOT` when set, else `.`; canonicalized.
- `resolve`: `Fs.resolveInside(root, path)`; any refusal or missing path is `OutsideWorkspace(path)`.
- `relative`: `Path.relativeTo` when the path is inside the root, else the path unchanged.
- `withTarget`:
  - `path`: resolved as above; the file must end in `.pudu` (`OutsideWorkspace` otherwise, since
    only source files are read); `directory` is the workspace root, and the file's text is read.
  - `source`: a new directory under the system temporary directory, prefix `pudu-mcp-`; the source
    is written at the path its module name implies ([[src/Domain/Code/SourceFile]]), `Main.pudu`
    when it declares none. `use` runs with that target and the directory is removed afterwards,
    whatever `use` answered.

## Negative Logic (Prohibited Paths)

- Nothing is ever written inside the workspace.
- A symbolic link inside the workspace pointing outside it is refused (resolution follows links).

## Edge Cases

- `path` naming a directory is refused here; `pudu_test` resolves directories itself with `resolve`.

## Depth

DEPTH 0.6 (DEEP).

## Grill Log

- **Q:** Delete the scratch directory even when the command failed? **A:** Yes; `use` answers a
  value rather than failing, so removal always runs after it.

## Referenced by

[[src/Services/_MOC]] · [[domain/Workspace]]
