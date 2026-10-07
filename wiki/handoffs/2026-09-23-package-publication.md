---
type: handoff
from_role: Forensic Guardian
to_role: Architect
status: complete
tags: [handoff, delivery]
---

# Package publication

## Done

- Issue #1 is the ready issue; `feature/1-initial-mcp-package` is branched from the `dev` baseline.
- `pudu.toml` carries every key the canonical library manifest has, with the language range
  `>=0.1.1 <0.2.0` and the module root `PuduLangMcp`.
- Against the 0.1.1 compiler: `pudu check`, `pudu fmt --check`, and `pudu lint` are clean over
  `src`, `test`, and `tools`; 20 suites pass with 464 assertions; `pudu build src/Main.pudu` links
  60 modules.
- The end-to-end suite reports failed checks by name when the server writes fewer replies than
  expected, instead of stopping at an index.
- A bounded run's wait for its output readers is capped at 250 ms after the child ends, so a
  grandchild holding the pipes (the Linux CI failure) no longer outlasts the deadline. Mutation on
  `Bounded.pudu`: 28 of 29 killed; the survivor is an unreachable fallback recorded in its Grill Log.

## Decided

- The package is released from `main` only; `main` and `dev` require the `checks` and `mutation`
  jobs and refuse force pushes and deletion.
- 0.1.0 shipped modules beside its root; 0.1.1 moved every module under `PuduLangMcp` (#5).
- Released 0.1.0 and 0.1.1 as tags with GitHub releases and the `pudu-package` topic. A consumer
  with its own `App.Context` installs `@chrismichaelps/pudu-lang-mcp@0.1.1` and serves it.
- www.pudu-lang.org lists the package at `/@chrismichaelps/pudu-lang-mcp`, with 0.1.1 as its
  latest release, after the site's package index was regenerated and deployed.

## Open / Remaining

- None for publication. A later release repeats `pudu release`, then the site's package index
  is regenerated and deployed so the listing shows it.

## Exact next action

None; publication is complete.

## Links

[[00-INDEX]] · [[architecture/TESTING]] · [[CHANGELOG]]
