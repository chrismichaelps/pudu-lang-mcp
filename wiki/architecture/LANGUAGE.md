---
type: language
tags: [architecture]
aliases: [Vocabulary]
---

# Architecture vocabulary

- **Module** — one `.pudu` file and its mirrored page under `wiki/src/`.
- **Interface** — a module's exported signatures; what callers may rely on.
- **Depth** — how much behaviour an interface hides relative to its size. Recorded per page.
- **Seam** — a boundary where one implementation can be replaced by another without editing
  callers. Here: [[seams/Toolchain]] and [[seams/Transport]].
- **Message** — one JSON-RPC value on one line of stdio.
- **Request / Notification** — a message with / without an `id`. Only requests are answered.
- **Reply** — the line written for a request: a result or an error.
- **Era** — modern (per-request `_meta`) or legacy (`initialize` handshake). See [[domain/ProtocolEra]].
- **Session** — the only process state: the era and, when legacy, the negotiated version.
- **Tool / Resource / Prompt** — MCP server features. See [[domain/Tool]], [[domain/Resource]],
  [[domain/Prompt]].
- **Toolchain** — the installed `pudu` executable and its standard library. See [[domain/Toolchain]].
- **Workspace** — the directory path arguments must stay inside. See [[domain/Workspace]].
- **Corpus** — the documentation compiled into the server from `Generated/Docs.pudu`.

## Referenced by

[[architecture/_MOC]]
