---
type: moc
tags: [moc]
---

# PuduLangMcp

The module root the package owns. Every module it ships is `PuduLangMcp` or under
`PuduLangMcp.*`, so a program that installs it keeps its own `App`, `Domain`, and other names.

- [[src/PuduLangMcp/App/_MOC]] — use cases: dispatch, discovery, tools, resources, prompts, completion.
- [[src/PuduLangMcp/Services/_MOC]] — effects: toolchain, processes, workspace, language server, reference index.
- [[src/PuduLangMcp/Domain/_MOC]] — pure protocol, catalogue, documentation, reference, and LSP logic.
- [[src/PuduLangMcp/Generated/_MOC]] — the compiled documentation corpus.
- [[src/PuduLangMcp/Utils/_MOC]] — JSON access, text bounds, URIs.
- [[src/PuduLangMcp/Constants/_MOC]] — protocol and server literals.
- [[src/PuduLangMcp/Errors/_MOC]] — protocol refusals and tool failures.

## Referenced by

[[src/_MOC]]
