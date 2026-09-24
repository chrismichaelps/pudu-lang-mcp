---
type: module
path: "@root/src/PuduLangMcp/Domain/Protocol/Negotiation.pudu"
fidelity: Active
domain: "[[domain/ProtocolEra]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.65
depth_status: DEEP
coupling: 4
interface_stability: 0.85
tags: [module, deep, critical]
aliases: [Negotiation]
---

# Negotiation

## Purpose

Decide, for one request and the current session, which protocol version governs it — or refuse
it — and negotiate the legacy version an `initialize` asks for. Implements
[[decisions/ADR-0001-dual-era-protocol]].

## Interface

### Signatures

```pudu
export type Session = Unopened | Legacy(Str)
export type Era = Modern | LegacyEra(Str)

export fn admit(session: &Session, method: Str, params: &Json.Json) -> Result[Era, RpcError.ProtocolError]
export fn negotiateLegacy(params: &Json.Json) -> Str
export fn isHandshake(method: Str) -> Bool          // "initialize"
```

### Linkage

- **Requires:** [[src/PuduLangMcp/Constants/Protocol]], [[src/PuduLangMcp/Errors/RpcError]], [[src/PuduLangMcp/Utils/JsonAccess]].
- **Consumed by:** [[src/PuduLangMcp/App/Dispatch]].

## Algorithm

`admit`:
1. Read `params._meta["io.modelcontextprotocol/protocolVersion"]`.
2. Present and not a string: `InvalidParams`.
3. Present and not `MODERN_VERSION`: `UnsupportedVersion(requested)`.
4. Present and modern: `params._meta["io.modelcontextprotocol/clientCapabilities"]` must be an
   object, else `InvalidParams`; answer `Modern`.
5. Absent and the method is `initialize`: `LegacyEra(negotiateLegacy(params))`.
6. Absent and the session is `Legacy(v)`: `LegacyEra(v)`.
7. Absent otherwise: `InvalidParams` naming the metadata key and every supported version.

`negotiateLegacy`: `params.protocolVersion` when it is a supported legacy version, else
`LATEST_LEGACY_VERSION`.

## Negative Logic (Prohibited Paths)

- A modern request never reads or changes the session.
- `_meta` that is present but not an object counts as absent metadata.

## Edge Cases

- A modern request for `initialize` is admitted as `Modern`; [[src/PuduLangMcp/App/Dispatch]] answers
  `-32601`, since the modern protocol has no such method.
- `initialize` with an unknown version succeeds at `2025-11-25`; the client decides whether to
  continue, as the legacy lifecycle prescribes.

## Depth

DEPTH 0.65 (DEEP). The whole compatibility matrix behind two functions.

## Grill Log

- **Q:** Should a second `initialize` be refused? **A:** No; it renegotiates. A client restarting
  its handshake on the same process gets the answer it asks for. _Rejected:_ refusing, which
  strands a client that lost its own state.

## Referenced by

[[src/PuduLangMcp/Domain/Protocol/_MOC]] · [[domain/ProtocolEra]]
