---
type: module
path: "@root/src/PuduLangMcp/App/Tools/Status.pudu"
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

- **Requires:** [[src/PuduLangMcp/App/Context]], [[src/PuduLangMcp/Constants/Server]], `Std.Sync`.
- **Consumed by:** [[src/PuduLangMcp/App/Tools/Registry]].

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

[[src/PuduLangMcp/App/Tools/_MOC]]
