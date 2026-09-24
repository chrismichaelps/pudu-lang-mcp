---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0005 — Tool results are text

## Context

A tool result may carry `structuredContent`, which must then match an `outputSchema`, and should
repeat itself as serialized JSON text for older clients.

## Decision

Every tool answers readable text content only. Invalid arguments and compiler failures are tool
execution errors (`isError: true`) with a message the model can act on; unknown tools and malformed
requests are protocol errors.

## Consequences

- One rendering per tool, no schema to keep in step with output, and no duplicated tokens.

## Rejected

- Structured content with output schemas: doubles the payload for the same information.

## Referenced by

[[decisions/_MOC]] · [[domain/Tool]]
