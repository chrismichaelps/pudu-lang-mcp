---
type: architecture
tags: [architecture, test]
aliases: [Testing]
---

# Testing

Every suite is a file under `test/`, mirroring the module it covers; `pudu test test` runs them all
and each suite names its failed checks on stderr.

| Level | Suites | What they prove |
| --- | --- | --- |
| Domain | `test/PuduLangMcp/Domain/**`, `test/PuduLangMcp/Utils`, `test/PuduLangMcp/Errors` | every rule of each pure module, success and refusal, boundaries and malformed input |
| Application | `test/PuduLangMcp/App/DispatchTest`, `test/PuduLangMcp/App/ToolsTest` | the whole protocol through a scripted [[seams/Toolchain]]: both eras, every method, every tool, confinement, scratch clean-up, timeouts, a missing compiler |
| Services | `test/PuduLangMcp/Services/ServicesTest` | real processes (status, streams, deadline, cap, UTF-8 cuts) and real directory layouts (library discovery, installed packages) |
| Package | `test/Package/LayoutTest` | every shipped module is the root `PuduLangMcp` or under it, is named after its path, and agrees with the manifest's `root` |
| End to end | `test/Integration/ServerTest` | the real server over stdio with the installed compiler: one reply line per request, real diagnostics, hover, confined runs, and reference |
| Mutation | [[tools/Mutate]] | the suites notice single-point changes to the source |

The end-to-end suite skips itself when no compiler is installed. It starts the server with
`PUDU_BIN`, or else the first `pudu` on `PATH`, which must satisfy the manifest's language range;
an older compiler writes no replies, and every check then fails by name.
The mutation gate runs on pull requests to `dev` and `main`; its deterministic sample must kill
every valid mutant. Before a release, the full source mutation pass is run and every survivor is
resolved by a regression test or removal of redundant logic.

## Referenced by

[[architecture/_MOC]]
