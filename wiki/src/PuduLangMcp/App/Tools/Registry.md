---
type: module
path: "@root/src/PuduLangMcp/App/Tools/Registry.pudu"
fidelity: Active
domain: "[[domain/Tool]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.55
depth_status: MODERATE
coupling: 8
interface_stability: 0.85
tags: [module, backbone]
aliases: [Tool Registry]
---

# Tool registry

## Purpose

`tools/list` and `tools/call`: the catalogue paginated, and a call validated and routed to its
handler, with the answer shaped as a tool result.

## Interface

### Signatures

```pudu
export fn list(context: &Context.Context, params: &Json.Json) -> Result[Json.Json, RpcError.ProtocolError]
export fn call(context: &Context.Context, params: &Json.Json) -> Result[Json.Json, RpcError.ProtocolError]
export fn toolResult(text: Str, isError: Bool) -> Json.Json
export fn handlers() -> Map[Str, fn(&Context.Context, &Arguments.Args) -> Result[Str, ToolError.ToolFailure]]
```

### Linkage

- **Requires:** [[src/PuduLangMcp/Domain/Catalog/Tools]], [[src/PuduLangMcp/Domain/Catalog/ToolSpec]], [[src/PuduLangMcp/Domain/Catalog/Arguments]], [[src/PuduLangMcp/Domain/Protocol/Pagination]], [[src/PuduLangMcp/Domain/Rpc/Reply]], every `App/Tools/*` handler.
- **Consumed by:** [[src/PuduLangMcp/App/Dispatch]].

## Algorithm

- `list`: page the catalogue ([[src/PuduLangMcp/Domain/Protocol/Pagination]], `PAGE_SIZE`) and answer
  `{tools, nextCursor?}` with the list caching hint.
- `call`: `params.name` must be a string (`InvalidParams`) naming a tool in the catalogue
  (`InvalidParams("Unknown tool: …")`), and the tool must have a handler in `handlers()`.
  Arguments are validated; a failure answers `toolResult(explain, true)`. The handler's `Ok(text)`
  answers `toolResult(text, false)`, its `Err` answers `toolResult(explain, true)`.

## Negative Logic (Prohibited Paths)

- A handler never sees unvalidated arguments.
- No tool result is cached: tool calls are not cacheable operations.

## Edge Cases

- `arguments` absent is `{}`.

## Depth

DEPTH 0.55.

## Grill Log

- **Q:** Why is the handler table a function, not a `const`? **A:** A `const` initialiser may
  not name another module's functions; the table is still one `mapOf` literal, built per call.

- **Q:** Keep handlers beside specs? **A:** No; specs are pure data in `Domain`, handlers are
  effects in `App`. `handlers()` joins them by name, and a test asserts every spec has a handler.

## Referenced by

[[src/PuduLangMcp/App/Tools/_MOC]] · [[src/PuduLangMcp/App/Dispatch]]
