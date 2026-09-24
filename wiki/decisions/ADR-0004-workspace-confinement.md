---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0004 — Confine file access to one workspace

## Context

Tool arguments come from a model and are untrusted. A path argument could name any file on the
machine.

## Decision

Path arguments are resolved with `Fs.resolveInside` against the workspace root (`PUDU_MCP_ROOT`,
else the directory the server started in). A path outside it, or one that does not exist, is a tool
execution error. Inline source is written to a fresh private directory under the system temporary
directory and removed after the command. `pudu_run` always passes `--confined`.

## Consequences

- A symbolic link inside the workspace that points outside it is refused.
- The server never writes into the workspace.

## Rejected

- Trusting the client's roots: the Roots feature is deprecated and roots are advisory.

## Referenced by

[[decisions/_MOC]] · [[domain/Workspace]]
