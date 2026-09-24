---
type: module
path: "@root/src/App/Context.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MODERATE
coupling: 6
interface_stability: 0.8
tags: [module, backbone]
aliases: [Context]
---

# Context

## Purpose

Everything a request handler may use, assembled once by [[src/Main]]: the toolchain, the
workspace, the documentation corpus cut into sections, the importable module files, and the
reference index being built in the background.

## Interface

### Signatures

```pudu
export type IndexState = Building | Ready(Array[Entry.Entry]) | Broken(Str)

export type Context = {
  toolchain: Toolchain.Toolchain,
  workspace: Workspace.Workspace,
  chapters: Array[Chapter.Chapter],
  sections: Array[Chapter.Section],
  moduleFiles: Array[Reference.ModuleFile],
  docsRevision: Str,
  index: Sync.Cell[IndexState]
}

export fn create(toolchain: Toolchain.Toolchain, workspace: Workspace.Workspace, chapters: Array[Chapter.Chapter], docsRevision: Str) -> Context
export fn entries(context: &Context) -> Result[Array[Entry.Entry], ToolError.ToolFailure]
export fn indexDeadlineReached(now: Int, deadline: Int) -> Bool
```

### Linkage

- **Requires:** [[src/Services/Toolchain]], [[src/Services/Workspace]], [[src/Services/Reference]], [[src/Domain/Docs/Chapter]], [[src/Domain/Reference/Entry]], [[src/Errors/ToolError]], `Std.Concurrent`, `Std.Sync`.
- **Consumed by:** [[src/Main]], [[src/App/Dispatch]], every `App` handler.

## Algorithm

- `create` splits the corpus into sections, lists the module files, and starts one background task
  that builds the index ([[src/Services/Reference]]) and stores `Ready` or `Broken`. With no
  compiler the builder reports `Broken`.
- `entries` answers the index; while it is `Building` it waits in 50 ms steps for at most
  `INDEX_WAIT_MS`, then answers `Unavailable` saying the index is still being built. The deadline
  comparison is explicit and includes the exact deadline.

## Negative Logic (Prohibited Paths)

- Nothing in a request changes the context; only the index cell is written, once, by its builder.

## Edge Cases

- A request that needs no index never waits for it.
- A clock reading exactly at the deadline has expired.

## Depth

DEPTH 0.5.

## Grill Log

- **Q:** Build the index at start-up or on first search? **A:** At start-up, in the background:
  the time it takes overlaps the client's own start-up, and documentation and compiler tools
  answer meanwhile. _Rejected:_ building on the first search, which makes that one call slow.
- **Q:** How is the exact timeout boundary tested? **A:** Keep the comparison in a pure helper
  used by `entries`, and test readings before, at, and after the deadline. _Rejected:_ sleeping
  until a millisecond boundary in a test, which is timing dependent.

## Referenced by

[[src/App/_MOC]]
