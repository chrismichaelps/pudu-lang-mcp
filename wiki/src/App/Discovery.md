---
type: module
path: "@root/src/App/Discovery.pudu"
fidelity: Active
domain: "[[domain/ProtocolEra]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.3
depth_status: SHALLOW
coupling: 3
interface_stability: 0.9
tags: [module, shallow]
aliases: [Discovery]
---

# Discovery

## Purpose

What this server says about itself: `server/discover` for modern clients, the `initialize` result
for legacy ones, and the legacy `ping` answer.

## Interface

### Signatures

```pudu
export fn capabilities() -> Json.Json
export fn discover(context: &Context.Context, params: &Json.Json) -> Result[Json.Json, RpcError.ProtocolError]
export fn initialize(version: Str) -> Json.Json
export fn pong() -> Json.Json
```

### Linkage

- **Requires:** [[src/Constants/Protocol]], [[src/Constants/Server]], [[src/Domain/Rpc/Reply]].
- **Consumed by:** [[src/App/Dispatch]].

## Algorithm

- `capabilities`: `tools`, `resources`, `prompts`, and `completions`, each `{}`: no list-change
  notifications and no subscriptions.
- `discover`: `supportedVersions`, `capabilities`, `instructions`, with the list caching hint.
- `initialize(version)`: `protocolVersion`, `capabilities`, `serverInfo {name, title, version}`,
  `instructions`.
- `pong`: an empty complete result.

## Negative Logic (Prohibited Paths)

- No capability is declared that the server does not implement.

## Edge Cases

- None.

## Depth

DEPTH 0.3 (SHALLOW).

## Grill Log

- **Q:** Declare `logging`? **A:** No; it is deprecated, and diagnostics go to stderr.

## Referenced by

[[src/App/_MOC]]
