---
type: module
path: "@root/src/App/Dispatch.pudu"
fidelity: Active
domain: "[[domain/ProtocolEra]]"
grammar: "[[grammar/pudu]]"
seam: "[[seams/Transport]]"
depth_score: 0.7
depth_status: DEEP
coupling: 7
interface_stability: 0.9
tags: [module, deep, backbone, critical]
aliases: [Dispatch]
---

# Dispatch

## Purpose

The protocol core: one input line and the session in, the next session and every reply line out.
Pure except for what handlers do through the context.

## Interface

### Signatures

```pudu
export type State = { session: Negotiation.Session }

export fn initial() -> State
export fn respond(context: &Context.Context, state: &State, line: Str) -> (State, Array[Str])
export fn routes() -> Map[Str, fn(&Context.Context, &Json.Json) -> Result[Json.Json, RpcError.ProtocolError]]
```

### Linkage

- **Requires:** [[src/Domain/Rpc/Message]], [[src/Domain/Rpc/Reply]], [[src/Domain/Protocol/Negotiation]], [[src/App/Discovery]], [[src/App/Tools/Registry]], [[src/App/Resources]], [[src/App/Prompts]], [[src/App/Completion]], [[src/Constants/Server]].
- **Consumed by:** [[src/PuduLangMcp]].

## Algorithm

1. A line over `MAX_LINE_BYTES` bytes is refused with `InvalidRequest` and `id: null`, unparsed.
2. A blank line answers nothing.
3. Parse ([[src/Domain/Rpc/Message]]): a refusal answers its error; a client response or any
   notification answers nothing.
4. A request is admitted ([[src/Domain/Protocol/Negotiation]]); a refusal answers its error.
5. Admitted:
   - `initialize`: legacy answers `Discovery.initialize(v)` and the session becomes `Legacy(v)`;
     modern answers `MethodNotFound`.
   - `ping`: legacy answers `pong`; modern answers `MethodNotFound`.
   - any method in `routes()`: its handler's result or error.
   - anything else: `MethodNotFound`.

## Negative Logic (Prohibited Paths)

- Never writes to stdout itself; never answers a notification.
- Only `initialize` changes the session.

## Edge Cases

- `notifications/cancelled` is accepted and ignored: requests are answered in order before the next
  line is read, so a cancellation always arrives after its request finished.

## Depth

DEPTH 0.7 (DEEP).

## Grill Log

- **Q:** Why `routes()` rather than a `const` table? **A:** A `const` initialiser may not name
  another module's functions ([[grammar/pudu]]).

## Referenced by

[[src/App/_MOC]] · [[seams/Transport]]
