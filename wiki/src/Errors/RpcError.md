---
type: module
path: "@root/src/Errors/RpcError.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.45
depth_status: MODERATE
coupling: 2
interface_stability: 0.9
tags: [module]
aliases: [Protocol Error]
---

# RpcError

## Purpose

Every way a request is refused at the protocol level, and its JSON-RPC wire form (code, message,
optional data).

## Interface

### Signatures

```pudu
export type ProtocolError
  = ParseError(Str)                 // the line is not JSON
  | InvalidRequest(Str)             // JSON, but not a single JSON-RPC request or notification
  | MethodNotFound(Str)             // method name
  | InvalidParams(Str)              // what is wrong with the parameters
  | ResourceNotFound(Str)           // uri
  | UnsupportedVersion(Str)         // requested version
  | Internal(Str)

export fn code(problem: &ProtocolError) -> Int
export fn message(problem: &ProtocolError) -> Str
export fn data(problem: &ProtocolError) -> Option[Json.Json]
```

### Governance

- `UnsupportedVersion` data is `{ supported: [...], requested }`, supported modern first.
- `ResourceNotFound` is `-32602` with `data.uri`.

### Linkage

- **Requires:** [[src/Constants/Protocol]], `Std.Json`.
- **Consumed by:** [[src/Domain/Rpc/Reply]], [[src/Domain/Protocol/Negotiation]], `App/*`.

## Algorithm

`code` maps each case to its constant; `message` gives the case's fixed prefix plus its detail;
`data` answers only for the two cases that carry structured data.

## Negative Logic (Prohibited Paths)

- No error echoes a whole request back; details are bounded by the caller.

## Edge Cases

- `MethodNotFound("")` still names the problem ("Method not found: ").

## Depth

DEPTH 0.45. Small surface; hides the code table and data shapes from every caller.

## Grill Log

- **Q:** Should `ResourceNotFound` be folded into `InvalidParams`? **A:** No; it carries the URI
  as data, which the specification's example shows. _Rejected:_ a string-only invalid-params.

## Referenced by

[[src/Errors/_MOC]]
