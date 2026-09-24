---
type: handoff
from_role: Forensic Guardian
to_role: Architect
tags: [handoff, delivery]
---

# Initial package publication

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

- The package is released from `main` only, after `dev` is merged there by the maintainer.
- Pull request #2 merged into `dev` with both CI jobs green; `main` and `dev` require the `checks`
  and `mutation` jobs and refuse force pushes and deletion.

## Open / Remaining

- Merge `dev` into `main`, which closes #1.
- Release with `pudu release 0.1.0` from `main`, then confirm `pudu install
  @chrismichaelps/pudu-lang-mcp@0.1.0` resolves.

## Exact next action

Open a pull request from `dev` into `main` for the 0.1.0 release.

## Links

[[00-INDEX]] · [[architecture/TESTING]] · [[CHANGELOG]]
