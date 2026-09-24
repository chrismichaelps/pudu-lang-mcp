---
type: module
path: "@root/src/App/Tools/Status.pudu"
fidelity: Active
domain: "[[domain/Toolchain]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.25
depth_status: SHALLOW
coupling: 2
interface_stability: 0.85
tags: [module, shallow]
aliases: [Status Tool]
---

# Status tool

## Purpose

`pudu_toolchain`: what this server is working with.

## Interface

### Signatures

```pudu
export fn toolchain(context: &Context.Context, args: &Arguments.Args) -> Result[Str, ToolError.ToolFailure]
```

### Linkage

- **Requires:** [[src/App/Context]], [[src/Constants/Server]], `Std.Sync`.
- **Consumed by:** [[src/App/Tools/Registry]].

## Algorithm

One line each: server name and version; compiler path and version, or "not found" with the
install hint; standard library root; workspace root; importable modules count; the reference
index state (building, ready with its declaration count, or its failure); documentation revision
and document count.

## Negative Logic (Prohibited Paths)

- Never fails: every missing piece is reported as missing.

## Edge Cases

- No compiler: the compiler line carries the install hint from `ToolchainMissing`.

## Depth

DEPTH 0.25 (SHALLOW).

## Grill Log

- **Q:** Include environment variables? **A:** Only their effect (paths), never their values
  wholesale; an environment can hold secrets.

## Referenced by

[[src/App/Tools/_MOC]]
