---
type: module
path: "@root/src/Utils/JsonAccess.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.35
depth_status: SHALLOW
coupling: 0
interface_stability: 0.9
tags: [module, shallow]
aliases: [Json Access]
---

# JsonAccess

## Purpose

Read typed members out of `Std.Json` values in one call each, and build the small JSON shapes the
protocol repeats.

## Interface

### Signatures

```pudu
export fn text(value: &Json.Json, name: Str) -> Option[Str]
export fn integer(value: &Json.Json, name: Str) -> Option[Int]
export fn boolean(value: &Json.Json, name: Str) -> Option[Bool]
export fn member(value: &Json.Json, name: Str) -> Option[Json.Json]
export fn members(value: &Json.Json) -> Array[(Str, Json.Json)]   // empty for a non-object
export fn kindOf(value: &Json.Json) -> Str                         // "string", "integer", ...
export fn texts(values: &Array[Str]) -> Json.Json                  // JSON list of strings
export fn pathText(value: &Json.Json, steps: &Array[Str]) -> Option[Str]
export fn pathInt(value: &Json.Json, steps: &Array[Str]) -> Option[Int]
```

### Linkage

- **Requires:** `Std.Json`.
- **Consumed by:** [[src/Domain/Rpc/Message]], [[src/Domain/Protocol/Negotiation]], [[src/Domain/Catalog/Arguments]], `App/*`.

## Algorithm

`member` is `Json.field` and the `path*` readers follow `Json.path`; typed readers compose it with the matching `Json.as*`. `kindOf` names
the JSON Schema type of a value: `Number` is "integer", `Fractional` is "number".

## Negative Logic (Prohibited Paths)

- A typed reader never coerces: `"3"` is not an integer.

## Edge Cases

- Reading a member of a non-object answers `None`.

## Depth

DEPTH 0.35.

## Grill Log

- **Q:** Keep an object predicate used only by its own tests? **A:** No; callers use typed JSON
  accessors and the predicate adds no domain rule. _Rejected:_ retaining dead production code.
- **Q:** Should `integer` accept a fractional value that happens to be whole? **A:** No; the
  decoder keeps `2.0` as `Fractional`, and JSON Schema's `integer` accepts it, but no argument
  here is a count a client writes as `2.0`. _Rejected:_ parsing `Fractional` text.

## Referenced by

[[src/Utils/_MOC]]
