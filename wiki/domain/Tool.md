---
type: domain
tags: [domain]
---

# Tool

A named operation with a JSON Schema for its arguments and annotations (`readOnlyHint`,
`destructiveHint`, `idempotentHint`, `openWorldHint`) — every tool declares all four. Names are
`pudu_` plus a snake-case verb phrase. `tools/list` answers them in one fixed order.

Arguments are validated before a tool runs: the value must be an object, every required property
present, every property of its declared type, and no undeclared property. A violation is a tool
execution error naming the property ([[decisions/ADR-0005-text-tool-results]]).

## Referenced by

[[domain/_MOC]] · [[architecture/LANGUAGE]]
