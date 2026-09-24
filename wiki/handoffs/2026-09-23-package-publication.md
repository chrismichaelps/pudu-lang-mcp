---
type: handoff
from_role: Forensic Guardian
to_role: Architect
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

- The package is released from `main` only.
- 0.1.0 was released and installs, but its modules sat beside the root and collided with a
  consumer's own `App` and `Domain` modules. #5 moves them under `PuduLangMcp` for 0.1.1.
- `main` and `dev` require the `checks` and `mutation` jobs and refuse force pushes and deletion.

## Open / Remaining

- Merge the #5 pull request into `dev`, then `dev` into `main`.
- Release 0.1.1 with `pudu release 0.1.1`, create its GitHub release, and confirm a consumer with
  its own `App.Context` installs and serves `@chrismichaelps/pudu-lang-mcp@0.1.1`.

## Exact next action

Merge the pull request for #5 into `dev` once CI is green.

## Links

[[00-INDEX]] · [[architecture/TESTING]] · [[CHANGELOG]]
