# Security policy

## Reporting a vulnerability

Report a suspected vulnerability privately through GitHub's
[security advisory form](https://github.com/chrismichaelps/pudu-lang-mcp/security/advisories/new),
or by email to <chrisperezsantiago1@gmail.com> with `SECURITY` in the subject.

Please do not open a public issue for a vulnerability. Include the server version
(`pudu_toolchain` reports it), the `pudu` version, the platform, and the smallest sequence of
messages that shows the problem.

You can expect an acknowledgement within seven days and a decision on whether the report is
accepted within thirty.

## What is in scope

The server runs the Pudu compiler on code and paths that a model chooses. A report is in scope
when a request can make the server do more than the tool it names says:

- Reading or writing a file outside the workspace (`PUDU_MCP_ROOT`), including through `..`,
  absolute paths, or symbolic links.
- `pudu_run` executing a program without `--confined`.
- Any tool writing into the workspace.
- A message that makes the server hang, or grow without bound, instead of answering within its
  deadlines and output caps.
- Anything other than protocol messages reaching standard output.

## What is not in scope

- `pudu_test` running the workspace's own tests with the workspace's permissions. That is what the
  tool is for, and its annotations say so.
- Vulnerabilities in the Pudu compiler itself; report those to
  [pudu-lang](https://github.com/chrismichaelps/pudu-lang/security).

## Supported versions

| Version | Supported |
| ------- | --------- |
| 0.1.x   | Yes       |
