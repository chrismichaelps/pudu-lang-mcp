---
type: module
path: "@root/src/Errors/ToolError.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MODERATE
coupling: 1
interface_stability: 0.85
tags: [module]
aliases: [Tool Failure]
---

# ToolError

## Purpose

Failures a tool reports as a result with `isError: true`: things a model can correct by changing
its arguments or by telling the user what is missing ([[decisions/ADR-0005-text-tool-results]]).

## Interface

### Signatures

```pudu
export type ToolFailure
  = MissingArgument(Str)
  | WrongType(Str, Str)             // argument, expected type
  | UnknownArgument(Str)
  | OutOfRange(Str, Str)            // argument, allowed range
  | NotAnObject
  | ExactlyOne(Array[Str])          // names of which exactly one must be given
  | ToolchainMissing
  | OutsideWorkspace(Str)
  | NotFound(Str)                   // what was looked for
  | TimedOut(Str, Int)              // command, milliseconds
  | CommandFailed(Str)              // a command could not be started
  | Unavailable(Str)                // a feature cannot run here, and why

export fn explain(failure: &ToolFailure) -> Str
```

### Linkage

- **Requires:** [[src/Constants/Server]].
- **Consumed by:** [[src/Domain/Catalog/Arguments]], [[src/Services/Workspace]], `App/Tools/*`.

## Algorithm

One sentence per case, each ending with what to do next.

## Negative Logic (Prohibited Paths)

- A compiler that ran and reported diagnostics is not a `ToolFailure`: diagnostics are the answer.

## Edge Cases

- `ToolchainMissing` names both `PUDU_BIN` and the download page.

## Depth

DEPTH 0.4.

## Grill Log

- **Q:** Is a failed check a tool error? **A:** No. The check ran; its diagnostics are the result
  and `isError` stays false. _Rejected:_ `isError` on non-zero status, which reads to a model as
  "the tool broke".

## Referenced by

[[src/Errors/_MOC]]
