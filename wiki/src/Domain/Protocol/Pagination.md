---
type: module
path: "@root/src/Domain/Protocol/Pagination.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MODERATE
coupling: 2
interface_stability: 0.9
tags: [module]
aliases: [Pagination]
---

# Pagination

## Purpose

Cut a list result into pages with opaque cursors, and refuse a cursor this server did not issue.

## Interface

### Signatures

```pudu
export fn cursorOf(params: &Json.Json) -> Result[Option[Str], RpcError.ProtocolError]
export fn page[T](items: &Array[T], cursor: &Option[Str], size: Int) -> Result[(Array[T], Option[Str]), RpcError.ProtocolError]
```

### Linkage

- **Requires:** [[src/Errors/RpcError]], [[src/Utils/JsonAccess]], `Std.Text`.
- **Consumed by:** [[src/App/Tools/Registry]], [[src/App/Resources]], [[src/App/Prompts]].

## Algorithm

- A cursor is `"offset:"` followed by a decimal offset.
- `cursorOf`: `params.cursor` absent is `None`; a string is `Some`; anything else is `InvalidParams`.
- `page`: no cursor starts at 0. A cursor that does not parse, or whose offset is past the end of
  the list, is `InvalidParams("invalid cursor")`. The page is `size` items from the offset, ending at
  `Math.min(offset + size, length)`; the next cursor is present only when items remain.

## Negative Logic (Prohibited Paths)

- No negative or out-of-range offsets are served.

## Edge Cases

- An empty list with no cursor answers an empty page and no next cursor.
- A cursor at exactly the list's length is invalid: this server never issues it.

## Depth

DEPTH 0.5.

## Grill Log

- **Q:** Encode the offset (base64)? **A:** No; opacity is a client obligation, not a secret, and
  a readable cursor is easier to diagnose. _Rejected:_ encoded cursors.

## Referenced by

[[src/Domain/Protocol/_MOC]]
