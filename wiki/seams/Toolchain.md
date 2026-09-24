---
type: seam
capacity: BACKBONE
tags: [seam, backbone]
---

# Toolchain (seam)

## Classification

Effect boundary between the protocol core and the installed compiler. A record of functions,
`Services.Toolchain.Toolchain`, built once by `Main`.

## Adapters

- **Installed** — [[src/Services/Toolchain]] over [[src/Services/Process/Bounded]].
- **Scripted** — built in tests: each function answers a fixed `Outcome`, so dispatch, argument
  handling, and rendering are tested without a compiler.

## Health

Every call is bounded by a deadline and an output cap. Nothing crosses the seam but text and
exit status.

## Referenced by

[[seams/_MOC]] · [[decisions/ADR-0003-toolchain-subprocess]] · [[domain/Toolchain]]
