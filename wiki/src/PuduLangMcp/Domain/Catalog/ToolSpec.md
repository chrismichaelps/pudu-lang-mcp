---
type: module
path: "@root/src/PuduLangMcp/Domain/Catalog/ToolSpec.pudu"
fidelity: Active
domain: "[[domain/Tool]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MODERATE
coupling: 2
interface_stability: 0.8
tags: [module]
aliases: [ToolSpec]
---

# ToolSpec

## Purpose

Describe a tool once, as data, and derive both its `tools/list` JSON (schema and annotations) and
the rules [[src/PuduLangMcp/Domain/Catalog/Arguments]] validates against.

## Interface

### Signatures

```pudu
export type Kind = TextKind | IntegerKind | BooleanKind
export type Property = { name: Str, kind: Kind, description: Str, required: Bool, minimum: Option[Int], maximum: Option[Int] }
export type Annotations = { readOnly: Bool, destructive: Bool, idempotent: Bool, openWorld: Bool }
export type ToolSpec = {
  name: Str, title: Str, description: Str,
  properties: Array[Property],
  exactlyOne: Array[Str],       // at most one group: exactly one of these names must be given
  annotations: Annotations
}

export fn text(name: Str, description: Str, required: Bool) -> Property
export fn integer(name: Str, description: Str, required: Bool, minimum: Int, maximum: Int) -> Property
export fn toJson(spec: &ToolSpec) -> Json.Json
export fn kindName(kind: &Kind) -> Str
```

### Linkage

- **Requires:** `Std.Json`, [[src/PuduLangMcp/Utils/JsonAccess]].
- **Consumed by:** [[src/PuduLangMcp/Domain/Catalog/Tools]], [[src/PuduLangMcp/Domain/Catalog/Arguments]].

## Algorithm

`toJson` writes `name`, `title`, `description`, `inputSchema` and `annotations`:

- `inputSchema`: `type: object`, `additionalProperties: false`, `properties` in declaration order
  (each with `type`, `description`, and `minimum`/`maximum` when set), `required` when non-empty,
  and `oneOf: [{required:[a]}, {required:[b]}]` when `exactlyOne` is set.
- `annotations`: `title`, `readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`.

## Negative Logic (Prohibited Paths)

- No property is both required and in `exactlyOne`.

## Edge Cases

- A tool with no properties answers `{type: object, additionalProperties: false}`, the form the
  specification recommends.

## Depth

DEPTH 0.5.

## Grill Log

- **Q:** Model full JSON Schema? **A:** No: string, integer, boolean, ranges, and one exclusive
  pair cover every tool here, and the validator is exact for exactly what is declared.

## Referenced by

[[src/PuduLangMcp/Domain/Catalog/_MOC]]
