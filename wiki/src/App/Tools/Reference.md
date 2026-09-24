---
type: module
path: "@root/src/App/Tools/Reference.pudu"
fidelity: Active
domain: "[[domain/Tool]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.45
depth_status: MODERATE
coupling: 5
interface_stability: 0.8
tags: [module]
aliases: [Reference Tools]
---

# Reference tools

## Purpose

`pudu_reference_search` and `pudu_module_reference`, answered from the installed toolchain
([[decisions/ADR-0006-reference-from-toolchain]]).

## Interface

### Signatures

```pudu
export fn search(context: &Context.Context, args: &Arguments.Args) -> Result[Str, ToolError.ToolFailure]
export fn moduleTool(context: &Context.Context, args: &Arguments.Args) -> Result[Str, ToolError.ToolFailure]
export fn moduleText(context: &Context.Context, moduleName: Str) -> Result[Str, ToolError.ToolFailure]
```

### Linkage

- **Requires:** [[src/App/Context]], [[src/Services/Reference]], [[src/Domain/Reference/Entry]], [[src/Domain/Catalog/Arguments]], [[src/Utils/TextBounds]].
- **Consumed by:** [[src/App/Tools/Registry]], [[src/App/Resources]].

## Algorithm

- `search`: a query containing `->` is a type shape: `pudu search <query> <every module file>`,
  whose output is answered as is, bounded. Otherwise the index ([[src/App/Context]] `entries`) is
  ranked with `Entry.search`; each hit renders its declaration and documentation.
- `moduleText`: find the module's file; unknown is `NotFound`, suggesting up to eight module names
  that contain the query case-insensitively. Otherwise `moduleEntries` rendered with
  `Entry.renderModule`, bounded to `DOC_CHARS`.

## Negative Logic (Prohibited Paths)

- Never answers a declaration the compiler does not report as public.

## Edge Cases

- A shape search with no match answers the compiler's own "no match" text.

## Depth

DEPTH 0.45.

## Grill Log

- **Q:** Why is the module handler named `module`? **A:** It is not; `module` is a keyword, so the
  handler is `moduleTool` and the tool name stays `pudu_module_reference`.

## Referenced by

[[src/App/Tools/_MOC]]
