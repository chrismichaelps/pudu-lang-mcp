---
type: moc
tags: [moc]
---

# Source map

One page per file under `src/`, mirroring it depth for depth.

- [[src/Main]] — the executable entry.
- [[src/PuduLangMcp]] — the package root: the stdio loop.
- [[src/App/_MOC]] — use cases: dispatch, discovery, tools, resources, prompts, completion.
- [[src/Services/_MOC]] — effects: toolchain, processes, workspace, language server, reference index.
- [[src/Domain/_MOC]] — pure protocol, catalogue, documentation, reference, and LSP logic.
- [[src/Generated/_MOC]] — the compiled documentation corpus.
- [[src/Utils/_MOC]] — JSON access, text bounds, URIs.
- [[src/Constants/_MOC]] — protocol and server literals.
- [[src/Errors/_MOC]] — protocol refusals and tool failures.

Tools outside `src/`: [[tools/SyncDocs]] (corpus generator) and [[tools/Mutate]] (mutation testing).

## Referenced by

[[00-INDEX]]
