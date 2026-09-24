---
type: module
path: "@root/src/PuduLangMcp/App/Resources.pudu"
fidelity: Active
domain: "[[domain/Resource]]"
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MODERATE
coupling: 6
interface_stability: 0.8
tags: [module]
aliases: [Resources]
---

# Resources

## Purpose

`resources/list`, `resources/templates/list`, and `resources/read` for the documentation corpus and
the installed modules' reference.

## Interface

### Signatures

```pudu
export fn list(context: &Context.Context, params: &Json.Json) -> Result[Json.Json, RpcError.ProtocolError]
export fn templates(context: &Context.Context, params: &Json.Json) -> Result[Json.Json, RpcError.ProtocolError]
export fn read(context: &Context.Context, params: &Json.Json) -> Result[Json.Json, RpcError.ProtocolError]
export const INDEX_URI: Str               // "pudu://docs/index"
export const REFERENCE_PREFIX: Str        // "pudu://reference/"
```

### Linkage

- **Requires:** [[src/PuduLangMcp/App/Context]], [[src/PuduLangMcp/App/Tools/Reference]], [[src/PuduLangMcp/Domain/Docs/Chapter]], [[src/PuduLangMcp/Domain/Protocol/Pagination]], [[src/PuduLangMcp/Domain/Rpc/Reply]], [[src/PuduLangMcp/Utils/Uri]].
- **Consumed by:** [[src/PuduLangMcp/App/Dispatch]], [[src/PuduLangMcp/App/Completion]].

## Algorithm

- `list`: the index resource, then one resource per document, `pudu://<group>/<slug>`, with its
  title and `text/markdown`; paged by `PAGE_SIZE`; list caching hint.
- `templates`: `pudu://reference/{module}` (`text/plain`); list caching hint.
- `read`: `params.uri` must be a string. The index answers the table of contents; a document URI
  its Markdown (docs caching hint, public); a reference URI the module's reference (reference
  caching hint, private); a reference that cannot be read is `Internal` with the reason. Any other
  URI is `ResourceNotFound(uri)`.

## Negative Logic (Prohibited Paths)

- Never answers an empty `contents` array.

## Edge Cases

- `pudu://docs/` with an empty slug is not found.
- A path with no slash is refused before slicing; an empty group reaches the not-found answer
  through `Chapter.find`.

## Depth

DEPTH 0.5.

## Grill Log

- **Q:** Is a separate slash-position guard needed before document lookup? **A:** No;
  `Chapter.find` refuses empty groups and slugs, while a path with no slash needs a guard because
  `take(-1)` is invalid. _Rejected:_ the old `<= 0` guard, whose zero boundary changes no result.
- **Q:** List every module as a resource? **A:** No; 174 and growing with packages. A template
  plus completion ([[src/PuduLangMcp/App/Completion]]) names them on demand.

## Referenced by

[[src/PuduLangMcp/App/_MOC]]
