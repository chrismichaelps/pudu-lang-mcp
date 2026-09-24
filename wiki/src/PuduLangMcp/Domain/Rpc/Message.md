---
type: module
path: "@root/src/PuduLangMcp/Domain/Rpc/Message.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
seam: "[[seams/Transport]]"
depth_score: 0.6
depth_status: DEEP
coupling: 3
interface_stability: 0.9
tags: [module, deep]
aliases: [Message]
---

# Message

## Purpose

Classify one line of input as a request, a notification, a response from the client, or a refusal
carrying the protocol error to answer with.

## Interface

### Signatures

```pudu
export type RequestId = NumberId(Int) | TextId(Str)

export type Incoming
  = Request(RequestId, Str, Json.Json)    // id, method, params (an object; {} when absent)
  | Notification(Str, Json.Json)          // method, params
  | ClientResponse                        // has result or error; this server sends no requests
  | Refused(Option[RequestId], RpcError.ProtocolError)

export fn parse(line: Str) -> Incoming
export fn idJson(id: &RequestId) -> Json.Json
```

### Governance

- Only a request is ever answered; a `Refused` without an id is answered with `"id": null`.

### Linkage

- **Requires:** [[src/PuduLangMcp/Errors/RpcError]], [[src/PuduLangMcp/Constants/Protocol]], [[src/PuduLangMcp/Utils/JsonAccess]].
- **Consumed by:** [[src/PuduLangMcp/App/Dispatch]].

## Algorithm

1. Decode; failure is `Refused(None, ParseError(reason))`.
2. A JSON array is `Refused(None, InvalidRequest)`: batching is not part of MCP.
3. A non-object is `Refused(None, InvalidRequest)`.
4. Read `id` when present: an integer or string is kept; `null`, a fraction, or any other kind is
   `Refused(None, InvalidRequest)`.
5. `jsonrpc` must be the string `"2.0"`, else `Refused(id, InvalidRequest)`.
6. No `method` but `result` or `error` present is `ClientResponse`.
7. `method` must be a string, else `Refused(id, InvalidRequest)`.
8. `params`, when present, must be an object; else `Refused(id, InvalidParams)` for a request and
   a notification with `{}` params otherwise (a notification is never answered).
9. With an id: `Request`; without: `Notification`.

## Negative Logic (Prohibited Paths)

- Never answers a notification whose envelope is valid, whatever its params. A line that is not a
  valid JSON-RPC envelope is refused even without an id, as JSON-RPC 2.0 requires.
- Never treats a string id `"1"` and integer id `1` as the same id.

## Edge Cases

- Leading or trailing whitespace around the JSON is accepted.
- An empty line is a parse error.
- `params: null` on a request is invalid params.

## Depth

DEPTH 0.6 (DEEP). Nine rules behind one function.

## Grill Log

- **Q:** Accept `params` as an array (JSON-RPC by-position)? **A:** No; every MCP method takes an
  object. _Rejected:_ positional parameters.
- **Q:** Answer a line whose id is `null`? **A:** Yes, with `-32600` and `id: null`; the sender
  broke the rule that ids must not be null and deserves to learn it.

## Referenced by

[[src/PuduLangMcp/Domain/Rpc/_MOC]] · [[src/PuduLangMcp/Domain/Rpc/Reply]]
