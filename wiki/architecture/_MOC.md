---
type: moc
tags: [moc, architecture]
aliases: [Architecture]
---

# Architecture

## Shape

One process, one stdio connection, one message per line. `Main` reads a line, hands it to
[[src/App/Dispatch|Dispatch]] with the current [[domain/ProtocolEra|session]], writes every
reply line it returns, and stops at end of input. Everything between the line and the reply is a
pure function except the calls made through the [[seams/Toolchain]] record.

Dependencies point inward, from `Main` to `App`, from `App` to `Services` and `Domain`, and from
those to the leaf layers:

| Layer | Holds | May import |
| --- | --- | --- |
| `Constants/` | protocol and server literals | std only |
| `Errors/` | typed failures and their wire form | Constants, std |
| `Utils/` | small pure helpers (JSON access, text bounds, URIs) | std only |
| `Generated/` | the documentation corpus, written by `tools/SyncDocs.pudu` | Domain types |
| `Domain/` | pure protocol, catalogue, documentation, and LSP logic | Utils, Constants, Errors, Generated |
| `Services/` | effects: processes, files, environment | Domain, Utils, Constants, Errors |
| `App/` | use cases that answer one method each | Domain, Services, Utils, Constants, Errors |
| `Main` | composition root and the stdio loop | App, Services |

## Protocol surface

Dual-era per [[decisions/ADR-0001-dual-era-protocol]]:

- **Modern** (`2026-07-28`): every request carries `io.modelcontextprotocol/protocolVersion` and
  `io.modelcontextprotocol/clientCapabilities` in `params._meta`. Results carry
  `resultType: "complete"` and `_meta["io.modelcontextprotocol/serverInfo"]`; cacheable results
  carry `ttlMs` and `cacheScope`.
- **Legacy** (`2025-11-25`, `2025-06-18`, `2025-03-26`, `2024-11-05`): `initialize` fixes the
  version for the rest of the process; `ping` answers `{}`.

Methods: `server/discover`, `initialize`, `ping` (legacy), `tools/list`, `tools/call`,
`resources/list`, `resources/read`, `resources/templates/list`, `prompts/list`, `prompts/get`,
`completion/complete`. Notifications (`notifications/initialized`, `notifications/cancelled`) are
accepted and never answered.

## Pages

- [[architecture/LANGUAGE]] — the vocabulary every page uses.
- [[grammar/pudu]] — the language rules the code follows.
- [[architecture/TESTING]] — the test levels and what each proves.

## Referenced by

[[00-INDEX]] · [[grammar/pudu]]
