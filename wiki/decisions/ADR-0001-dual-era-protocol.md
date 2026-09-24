---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0001 — Serve both protocol eras

## Context

MCP `2026-07-28` removed the `initialize` handshake: each request carries its version and client
capabilities in `_meta`, and `server/discover` is mandatory. The agents this server is for still
open with `initialize` at `2025-11-25` or earlier.

## Decision

The server is dual-era. A request whose `params._meta` names
`io.modelcontextprotocol/protocolVersion` is served statelessly under the modern rules. An
`initialize` request selects legacy semantics for the rest of the process, at the requested
version when supported and otherwise at `2025-11-25`. Before either, any other request is refused
with `-32602`, naming the supported versions, as the modern rules require for a request missing
its metadata.

## Consequences

- The only process state is [[domain/ProtocolEra|the session]]: era and legacy version.
- Modern requests are validated on every call; a legacy session never rechecks.
- Result shapes are shared. Modern fields (`resultType`, `ttlMs`, `cacheScope`, `_meta`) are
  harmless extra members for a legacy client.

## Rejected

- Modern only: every current agent would fail at `initialize`.
- Legacy only: fails modern clients deterministically at `server/discover`.

## Referenced by

[[decisions/_MOC]] · [[architecture/_MOC]] · [[domain/ProtocolEra]]
