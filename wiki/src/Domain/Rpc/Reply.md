---
type: module
path: "@root/src/Domain/Rpc/Reply.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
seam: "[[seams/Transport]]"
depth_score: 0.5
depth_status: MODERATE
coupling: 3
interface_stability: 0.9
tags: [module]
aliases: [Reply]
---

# Reply

## Purpose

Encode results and errors as single JSON lines, and give every complete result the fields the
modern protocol requires.

## Interface

### Signatures

```pudu
export type Cache = NoCache | Cached(Int, Str)       // ttlMs, cacheScope

export fn complete(fields: &Array[(Str, Json.Json)], cache: &Cache) -> Json.Json
export fn result(id: &Message.RequestId, body: &Json.Json) -> Str
export fn failure(id: &Option[Message.RequestId], problem: &RpcError.ProtocolError) -> Str
```

### Governance

- `complete` adds `resultType: "complete"` and `_meta` with `serverInfo {name, title, version}`;
  a `Cached` hint adds `ttlMs` and `cacheScope`.
- Output is `Json.encode`, which escapes line breaks, so every reply is exactly one line.

### Linkage

- **Requires:** [[src/Domain/Rpc/Message]], [[src/Errors/RpcError]], [[src/Constants/Protocol]], [[src/Constants/Server]].
- **Consumed by:** [[src/App/Dispatch]] and every `App` handler that builds a body.

## Algorithm

- `result`: `{"jsonrpc":"2.0","id":…,"result":body}`.
- `failure`: `{"jsonrpc":"2.0","id":… or null,"error":{"code","message","data"?}}`.

## Negative Logic (Prohibited Paths)

- No reply carries both `result` and `error`.
- `ttlMs` is never negative.

## Edge Cases

- A `Cached` hint with a negative ttl is written as `0`.

## Depth

DEPTH 0.5.

## Grill Log

- **Q:** Where are `resultType` and `serverInfo` added? **A:** Once, here. _Rationale:_ every
  handler would otherwise repeat them and one would forget. _Rejected:_ per-handler fields.

## Referenced by

[[src/Domain/Rpc/_MOC]]
