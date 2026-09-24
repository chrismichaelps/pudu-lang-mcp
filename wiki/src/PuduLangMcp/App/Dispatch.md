---
type: module
path: "@root/src/PuduLangMcp/App/Dispatch.pudu"
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

- **Requires:** [[src/PuduLangMcp/Domain/Rpc/Message]], [[src/PuduLangMcp/Domain/Rpc/Reply]], [[src/PuduLangMcp/Domain/Protocol/Negotiation]], [[src/PuduLangMcp/App/Discovery]], [[src/PuduLangMcp/App/Tools/Registry]], [[src/PuduLangMcp/App/Resources]], [[src/PuduLangMcp/App/Prompts]], [[src/PuduLangMcp/App/Completion]], [[src/PuduLangMcp/Constants/Server]].
- **Consumed by:** [[src/PuduLangMcp]].

## Algorithm

1. A line over `MAX_LINE_BYTES` bytes is refused with `InvalidRequest` and `id: null`, unparsed.
2. A blank line answers nothing.
3. Parse ([[src/PuduLangMcp/Domain/Rpc/Message]]): a refusal answers its error; a client response or any
   notification answers nothing.
4. A request is admitted ([[src/PuduLangMcp/Domain/Protocol/Negotiation]]); a refusal answers its error.
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

[[src/PuduLangMcp/App/_MOC]] · [[seams/Transport]]
