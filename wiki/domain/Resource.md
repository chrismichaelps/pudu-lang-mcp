---
type: domain
tags: [domain]
---

# Resource

Readable context identified by a URI.

- `pudu://docs/index` — the table of contents of the language documentation.
- `pudu://docs/<slug>` — one documentation chapter as Markdown.
- `pudu://stdlib/{module}` (template) — the reference for one standard-library module, from the
  installed toolchain.

An unknown URI is `-32602` with `data.uri`; never an empty `contents`.

## Referenced by

[[domain/_MOC]] · [[architecture/LANGUAGE]]
