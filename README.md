<p align="center">
  <img src="public/pudu-lang-short.png" alt="Pudu" width="120">
</p>

<p align="center">
  <a href="https://www.pudu-lang.org/">Pudu</a> |
  <a href="https://www.pudu-lang.org/docs">Documentation</a> |
  <a href="https://modelcontextprotocol.io/specification/2026-07-28">MCP specification</a> |
  <a href="CONTRIBUTING.md">Contributing</a>
</p>

# pudu-lang-mcp

A [Model Context Protocol](https://modelcontextprotocol.io) server for writing Pudu. It gives any
MCP client the Pudu language documentation, the API reference of the installed standard library
and packages, and the compiler's own answers: diagnostics, formatting, lint findings, confined runs,
tests, and language-server questions such as hover, definition, references, and completion.

Powered by Pudu: the server is itself a Pudu program and a Pudu package,
[`@chrismichaelps/pudu-lang-mcp`](https://www.pudu-lang.org/packages).

## Tools

| Area | Tools |
| --- | --- |
| Documentation | `pudu_docs_search`, `pudu_docs_read` |
| API reference | `pudu_reference_search`, `pudu_module_reference` |
| Compiler | `pudu_check`, `pudu_format`, `pudu_lint`, `pudu_run`, `pudu_test` |
| Language server | `pudu_hover`, `pudu_definition`, `pudu_references`, `pudu_completion`, `pudu_signature_help`, `pudu_code_actions`, `pudu_rename_preview`, `pudu_document_symbols`, `pudu_workspace_symbols` |
| Toolchain | `pudu_toolchain` |

Code is passed inline as `source`, or as a `path` inside the workspace. Positions are 1-based.
`pudu_run` always runs confined: no files, processes, network, or foreign code. Nothing writes to
the workspace.

The documentation is also offered as resources (`pudu://docs/<chapter>`, `pudu://examples/<name>`,
and more), each module's reference as `pudu://reference/{module}`, and four prompts for writing,
fixing, reviewing, and explaining Pudu code.

The server speaks MCP `2026-07-28` per request and the `initialize` handshake of `2025-11-25`,
`2025-06-18`, `2025-03-26`, and `2024-11-05`, over stdio.

## Installing

It needs [Pudu 0.1.1 or later](https://www.pudu-lang.org/download) on `PATH`.

```bash
git clone https://github.com/chrismichaelps/pudu-lang-mcp
cd pudu-lang-mcp
pudu build src/Main.pudu -o pudu-lang-mcp
```

Register the executable with your MCP client as a stdio server. Most clients read a configuration
of this shape:

```json
{
  "mcpServers": {
    "pudu": {
      "command": "/path/to/pudu-lang-mcp",
      "env": { "PUDU_MCP_ROOT": "/path/to/your/project" }
    }
  }
}
```

| Variable | Meaning | Default |
| --- | --- | --- |
| `PUDU_MCP_ROOT` | The workspace that `path` arguments must stay inside | the directory the server starts in |
| `PUDU_BIN` | The `pudu` executable to use | the first `pudu` on `PATH` |
| `PUDU_LIB` | The standard library to read the reference from | the one installed beside `pudu` |

## Developing

```bash
pudu test test                          # unit, application, and end-to-end suites
pudu run tools/Mutate.pudu --every 3 --threshold 100  # pull-request mutation gate
pudu fmt --check src test tools && pudu lint src test tools
```

Before a release, run the full mutation pass with
`pudu run tools/Mutate.pudu --threshold 100`.

The design lives in the [wiki vault](wiki/00-INDEX.md): one page per source file, the decisions
behind the protocol and toolchain choices, and the Pudu grammar rules the code follows.

## License

[Apache License 2.0](LICENSE).
