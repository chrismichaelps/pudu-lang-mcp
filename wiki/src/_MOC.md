---
type: moc
tags: [moc]
---

# Source map

One page per file under `src/`, mirroring it depth for depth.

- [[src/Main]] — the executable entry.
- [[src/PuduLangMcp]] — the package root: the stdio loop.
- [[src/PuduLangMcp/_MOC]] — every module the package owns, under its root `PuduLangMcp`.

Tools outside `src/`: [[tools/SyncDocs]] (corpus generator) and [[tools/Mutate]] (mutation testing).

## Referenced by

[[00-INDEX]]
