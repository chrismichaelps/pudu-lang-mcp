---
type: module
path: "@root/src/PuduLangMcp/App/Completion.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MODERATE
coupling: 4
interface_stability: 0.8
tags: [module]
aliases: [Completion]
---

# Completion

## Purpose

`completion/complete`: suggest module names for the reference template and documentation slugs
for the `pudu_explain` prompt's topic.

## Interface

### Signatures

```pudu
export fn complete(context: &Context.Context, params: &Json.Json) -> Result[Json.Json, RpcError.ProtocolError]
```

### Linkage

- **Requires:** [[src/PuduLangMcp/App/Context]], [[src/PuduLangMcp/App/Resources]], [[src/PuduLangMcp/Services/Reference]], [[src/PuduLangMcp/Domain/Docs/Chapter]], [[src/PuduLangMcp/Domain/Rpc/Reply]].
- **Consumed by:** [[src/PuduLangMcp/App/Dispatch]].

## Algorithm

1. `params.ref.type` and `params.argument.name`, `params.argument.value` must be strings
   (`InvalidParams`).
2. Candidates: for `ref/resource` with `uri` equal to the reference template and argument `module`,
   every importable module name; for `ref/prompt` named `pudu_explain` and argument `topic`, every
   docs slug; otherwise none.
3. Keep candidates starting with the value, case-insensitively; answer at most `MAX_VALUES` with
   `total` and `hasMore`.

## Negative Logic (Prohibited Paths)

- An unknown reference or argument answers an empty completion, not an error.

## Edge Cases

- An empty value answers the first `MAX_VALUES` candidates.

## Depth

DEPTH 0.4.

## Grill Log

- **Q:** Prefix or substring matching? **A:** Prefix, as the specification's examples do; a
  client types from the start of a value.

## Referenced by

[[src/PuduLangMcp/App/_MOC]]
