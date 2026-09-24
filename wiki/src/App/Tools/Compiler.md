---
type: module
path: "@root/src/App/Tools/Compiler.pudu"
fidelity: Active
domain: "[[domain/Tool]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MODERATE
coupling: 5
interface_stability: 0.8
tags: [module]
aliases: [Compiler Tools]
---

# Compiler tools

## Purpose

`pudu_check`, `pudu_format`, `pudu_lint`, `pudu_run`, and `pudu_test`: the compiler's own answers
for a piece of code or a workspace file.

## Interface

### Signatures

```pudu
export fn check(context: &Context.Context, args: &Arguments.Args) -> Result[Str, ToolError.ToolFailure]
export fn format(context: &Context.Context, args: &Arguments.Args) -> Result[Str, ToolError.ToolFailure]
export fn lint(context: &Context.Context, args: &Arguments.Args) -> Result[Str, ToolError.ToolFailure]
export fn run(context: &Context.Context, args: &Arguments.Args) -> Result[Str, ToolError.ToolFailure]
export fn test(context: &Context.Context, args: &Arguments.Args) -> Result[Str, ToolError.ToolFailure]
```

### Linkage

- **Requires:** [[src/App/Context]], [[src/Services/Workspace]], [[src/Services/Toolchain]], [[src/Domain/Catalog/Arguments]], [[src/Utils/TextBounds]], [[src/Constants/Server]].
- **Consumed by:** [[src/App/Tools/Registry]].

## Algorithm

Every command runs through `onTarget`: with no compiler, `ToolchainMissing`; otherwise
[[src/Services/Workspace]] `withTarget` places the code, and the command runs in the target's
directory on the file's path relative to it. Output is the command's standard output then its
standard error, trimmed, with the scratch directory's path removed, bounded to `DOC_CHARS`.

| Tool | Command | Answer |
| --- | --- | --- |
| `pudu_check` | `check <file>` | the compiler's report, "no diagnostics" included |
| `pudu_format` | `fmt --stdout <file>` | the formatted code; on failure "The code could not be formatted:" and the diagnostics |
| `pudu_lint` | `lint <file>` | the findings, or the linter's clean report |
| `pudu_run` | `run --confined <file>` | `exit status N`, then output; a stopped run says it was stopped after the deadline |
| `pudu_test` | `test [path]` in the workspace root | the test report |

`pudu_run` and `pudu_test` use `timeoutMs` (default `RUN_TIMEOUT_MS`); the others
`COMMAND_TIMEOUT_MS`. `pudu_test`'s `path`, when given, must resolve inside the workspace.

## Negative Logic (Prohibited Paths)

- A non-zero exit is an answer, not a tool failure ([[decisions/ADR-0005-text-tool-results]]).
- `pudu_run` never runs without `--confined`.

## Edge Cases

- A command stopped at its deadline answers its partial output after a line saying so.

## Depth

DEPTH 0.5.

## Grill Log

- **Q:** Run `pudu_test` confined? **A:** No; the project's suites are its own code, written to
  touch files and processes. The tool's annotations say so instead.

## Referenced by

[[src/App/Tools/_MOC]]
