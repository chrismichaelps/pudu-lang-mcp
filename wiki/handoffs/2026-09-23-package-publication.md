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

## Open / Remaining

- Review and merge the pull request for #1 into `dev`; CI runs the checks and the mutation sample.
- A release (`pudu release 0.1.0`) follows once `dev` reaches `main`.

## Exact next action

Review the pull request for #1 against `dev` and merge it once CI is green.

## Links

[[00-INDEX]] · [[architecture/TESTING]] · [[CHANGELOG]]
