---
type: domain
tags: [domain]
aliases: [Era, Session]
---

# Protocol era

The session is `Unopened`, `Legacy(version)`, or nothing more: modern requests never change it.

| Incoming | Session | Outcome |
| --- | --- | --- |
| request with `_meta` protocol version `2026-07-28` | any | served statelessly |
| request with `_meta` protocol version not supported | any | `-32022`, `data.supported`, `data.requested` |
| `_meta` version present, `clientCapabilities` absent or not an object | any | `-32602` |
| `initialize` without `_meta` version | `Unopened` or `Legacy` | legacy result; session becomes `Legacy(v)` |
| any other request without `_meta` version | `Legacy(v)` | served under `v` |
| any other request without `_meta` version | `Unopened` | `-32602` naming the supported versions |
| `ping` | `Legacy` | `{}` ; modern: `-32601` |

Legacy version negotiation: the requested version if it is one of `2025-11-25`, `2025-06-18`,
`2025-03-26`, `2024-11-05`; otherwise `2025-11-25`.

## Referenced by

[[domain/_MOC]] · [[decisions/ADR-0001-dual-era-protocol]] · [[architecture/LANGUAGE]]
