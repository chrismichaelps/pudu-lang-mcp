---
type: seam
capacity: EDGE
tags: [seam]
---

# Transport (seam)

## Classification

The stdio loop in [[src/Main]] is the only code that touches stdin and stdout. The core answers
`Dispatch.respond(session, line) -> (session, replies)`, so any line-framed channel could carry it.

## Referenced by

[[seams/_MOC]] · [[domain/Transport]]
