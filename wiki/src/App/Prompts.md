---
type: module
path: "@root/src/App/Prompts.pudu"
fidelity: Active
domain: "[[domain/Prompt]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.3
depth_status: SHALLOW
coupling: 3
interface_stability: 0.85
tags: [module, shallow]
aliases: [Prompts Handler]
---

# Prompts handler

## Purpose

`prompts/list` and `prompts/get` over [[src/Domain/Catalog/Prompts]].

## Interface

### Signatures

```pudu
export fn list(context: &Context.Context, params: &Json.Json) -> Result[Json.Json, RpcError.ProtocolError]
export fn get(context: &Context.Context, params: &Json.Json) -> Result[Json.Json, RpcError.ProtocolError]
```

### Linkage

- **Requires:** [[src/Domain/Catalog/Prompts]], [[src/Domain/Protocol/Pagination]], [[src/Domain/Rpc/Reply]].
- **Consumed by:** [[src/App/Dispatch]].

## Algorithm

- `list`: paged prompts; list caching hint.
- `get`: `params.name` must be a string; `params.arguments` (default `{}`) is rendered by
  `Prompts.render`; the result is `{description, messages: [{role: "user", content: {type: "text", text}}]}`.

## Negative Logic (Prohibited Paths)

- No prompt result is cached.

## Edge Cases

- `arguments` present but not an object is `InvalidParams`.

## Depth

DEPTH 0.3.

## Grill Log

- **Q:** One user message or several? **A:** One; the template is a single instruction.

## Referenced by

[[src/App/_MOC]]
