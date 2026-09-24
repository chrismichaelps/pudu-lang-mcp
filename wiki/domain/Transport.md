---
type: domain
tags: [domain]
---

# Transport

stdio, one JSON-RPC message per line, UTF-8. stdout carries protocol messages only; stderr carries
operator diagnostics. End of input ends the process with status 0. A line longer than
`MAX_LINE_BYTES` is refused as `-32600` without being parsed.

## Referenced by

[[domain/_MOC]] · [[seams/Transport]] · [[grammar/pudu]]
