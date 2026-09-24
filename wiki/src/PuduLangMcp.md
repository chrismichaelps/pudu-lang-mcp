---
type: module
path: "@root/src/PuduLangMcp.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
seam: "[[seams/Transport]]"
depth_score: 0.5
depth_status: MODERATE
coupling: 5
interface_stability: 0.9
tags: [module, backbone]
aliases: [PuduLangMcp, Package Root]
---

# PuduLangMcp (package root)

## Purpose

The package's public entry, named by `root` in `pudu.toml`: assemble the context from the
environment and serve MCP over stdio until input ends. A program that installs this package can
serve the same way with `PuduLangMcp.serve()`.

## Interface

### Signatures

```pudu
export fn serve() -> Int
```

### Linkage

- **Requires:** [[src/App/Context]], [[src/App/Dispatch]], [[src/Services/Toolchain]], [[src/Services/Workspace]], [[src/Generated/Docs]], `Std.Io`.
- **Consumed by:** [[src/Main]].

## Algorithm

1. Context from `Toolchain.locate()`, `Workspace.fromEnvironment()`, and the generated corpus.
2. Loop: read one line; at end of input answer 0; on a read failure write it to stderr and
   answer 1. Otherwise `Dispatch.respond`, keep the new state, and write each reply line. A reply
   that cannot be written ends the loop with 1: the client has gone.

## Negative Logic (Prohibited Paths)

- Nothing but reply lines reaches stdout.

## Edge Cases

- Input ending without a final line break still answers the last line.

## Depth

DEPTH 0.5.

## Grill Log

- **Q:** Why is the loop in the package root, not in `Main`? **A:** The root is what an installed
  package exposes; keeping the loop there lets another program embed the server, and leaves `Main`
  a one-line entry for `pudu run` and `pudu build`.

## Referenced by

[[src/_MOC]] · [[src/Main]] · [[seams/Transport]]
