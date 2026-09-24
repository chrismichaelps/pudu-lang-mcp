---
type: module
path: "@root/src/PuduLangMcp/Domain/Catalog/Arguments.pudu"
fidelity: Active
domain: "[[domain/Tool]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: DEEP
coupling: 3
interface_stability: 0.85
tags: [module, deep]
aliases: [Arguments]
---

# Arguments

## Purpose

Validate a `tools/call` `arguments` value against its [[src/PuduLangMcp/Domain/Catalog/ToolSpec]], and hand
tools typed values they no longer need to check.

## Interface

### Signatures

```pudu
export type Args = { values: Array[(Str, Json.Json)] }

export fn validate(spec: &ToolSpec.ToolSpec, arguments: &Json.Json) -> Result[Args, ToolError.ToolFailure]
export fn text(args: &Args, name: Str) -> Option[Str]
export fn integer(args: &Args, name: Str) -> Option[Int]
export fn integerOr(args: &Args, name: Str, fallback: Int) -> Int
```

### Linkage

- **Requires:** [[src/PuduLangMcp/Domain/Catalog/ToolSpec]], [[src/PuduLangMcp/Errors/ToolError]], [[src/PuduLangMcp/Utils/JsonAccess]].
- **Consumed by:** [[src/PuduLangMcp/App/Tools/Registry]] and every tool handler.

## Algorithm

1. `null` arguments are `{}`; any other non-object is `NotAnObject`.
2. Every member must be a declared property, else `UnknownArgument(name)` (first in member order).
3. Each declared property, in declaration order: absent and required is `MissingArgument`;
   present with the wrong JSON kind is `WrongType(name, kind)`; a text property that is required
   must not be blank (`WrongType(name, "a non-empty string")`); an integer outside
   `minimum..maximum` is `OutOfRange(name, "between a and b")`.
4. With an `exactlyOne` group, the number of its names present must be 1, else `ExactlyOne(group)`.

## Negative Logic (Prohibited Paths)

- No coercion between kinds; `"5"` is not an integer.
- A `null` member is the wrong kind, not an absent one.

## Edge Cases

- A blank optional text property is accepted and handled by the tool.
- Both `source` and `path` given, or neither: `ExactlyOne`.

## Depth

DEPTH 0.6 (DEEP).

## Grill Log

- **Q:** Refuse undeclared members? **A:** Yes; the schema says `additionalProperties: false`, and
  silently ignoring a misspelt `limt` hides the model's mistake. _Rejected:_ lenient extra members.

## Referenced by

[[src/PuduLangMcp/Domain/Catalog/_MOC]]
