---
type: module
path: "@root/src/PuduLangMcp/Constants/Protocol.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.2
depth_status: SHALLOW
coupling: 0
interface_stability: 0.9
tags: [module, shallow]
aliases: [Protocol Constants]
---

# Protocol constants

## Purpose

Every literal the MCP and JSON-RPC specifications fix: protocol versions, metadata keys, error
codes, and result fields. No other module spells these.

## Interface

### Signatures

```pudu
export const MODERN_VERSION: Str            // "2026-07-28"
export const LEGACY_VERSIONS: Array[Str]    // newest first
export const LATEST_LEGACY_VERSION: Str
export const JSONRPC_VERSION: Str
export const META_KEY: Str                  // "_meta"
export const META_PROTOCOL_VERSION: Str
export const META_CLIENT_CAPABILITIES: Str
export const META_SERVER_INFO: Str
export const PARSE_ERROR: Int               // -32700
export const INVALID_REQUEST: Int           // -32600
export const METHOD_NOT_FOUND: Int          // -32601
export const INVALID_PARAMS: Int            // -32602
export const INTERNAL_ERROR: Int            // -32603
export const UNSUPPORTED_PROTOCOL_VERSION: Int   // -32022
export const RESULT_COMPLETE: Str           // "complete"
export const CACHE_PUBLIC: Str
export const CACHE_PRIVATE: Str
export fn supportedVersions() -> Array[Str] // modern first, then legacy
export fn isLegacyVersion(version: Str) -> Bool
```

### Linkage

- **Requires:** std only.
- **Consumed by:** [[src/PuduLangMcp/Errors/RpcError]], [[src/PuduLangMcp/Domain/Protocol/Negotiation]], [[src/PuduLangMcp/Domain/Rpc/Reply]].

## Algorithm

Constants only; `isLegacyVersion` is membership in `LEGACY_VERSIONS`.

## Negative Logic (Prohibited Paths)

- No code emits a code in `-32000..-32019` or an undefined code in `-32020..-32099`.
- `-32002` (legacy resource-not-found) is never emitted.

## Edge Cases

- A version string differing only in case is a different, unsupported version.

## Depth

DEPTH 0.2 (SHALLOW). A vocabulary; its value is that nothing else spells these literals.

## Grill Log

- **Q:** Keep a metadata key that no request reads? **A:** No; the protocol accepts client info
  without naming that key in server logic. _Rejected:_ an unused exported constant.
- **Q:** Which legacy versions? **A:** `2025-11-25`, `2025-06-18`, `2025-03-26`, `2024-11-05`.
  _Rationale:_ the method and result shapes this server uses are unchanged across them.
  _Rejected:_ only `2025-11-25`, which strands older clients for no gain.

## Referenced by

[[src/PuduLangMcp/Constants/_MOC]]
