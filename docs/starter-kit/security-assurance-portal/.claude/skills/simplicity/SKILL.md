---
name: simplicity
description: Use when drafting spec.md or plan.md, choosing architecture, adding a dependency, service, abstraction or configuration option, or reviewing a PR that adds complexity. Keeps the system small enough for one engineer plus AI agents to maintain.
---

# Simplicity rules

The goal is feature parity with market leaders while staying small enough for a very small team to understand completely. Complexity is a cost that has to be justified in writing.

## Architecture defaults (change only with a written reason in spec.md)

- **One deployable application** (a modular monolith). At most one sidecar worker, and only if a required library exists in just one other language, such as document parsing.
- **PostgreSQL is the only database.** Use it for:
  - full-text search
  - **pgvector** for embeddings
  - a Postgres-backed job queue
  - sessions

  No Redis, Kafka, Elasticsearch, a separate vector database, or a separate search service unless measured load proves it's needed.
- **S3-compatible object storage** for files. The same code runs against local S3-compatible storage in development and any S3 service in production.
- **Single organization per deployment.** Don't build multi-tenant SaaS machinery.
- **One frontend stack**, used for both the staff console and the public trust center.
- **One LLM interface module.** Providers plug in behind it. Don't scatter provider SDK calls through the codebase.
- **Configuration through environment variables** with safe defaults. Feature flags only for features that are actually unfinished.

## Code rules

- Prefer boring, widely used libraries with large communities (current AI models know them well, too).
- Organize modules by feature: library, sources, projects, portal, ai, integrations, admin. Each module has a short README explaining its purpose and boundaries.
- Don't add an abstraction until there are at least two real uses for it. Three similar lines beat a premature framework.
- Don't add configuration for things nobody has asked to configure.
- Integrations share one small pattern (connector interface plus a job). Each integration lives in its own folder and can be deleted without touching the rest.
- Generated code (API types, ORM models) is regenerated from source and never hand-edited.
- A file over about 400 lines, or a function over about 60, is a signal to split it — not a hard rule.

## The complexity check (put it in spec.md and plan.md)

For every new service, dependency, background process or configuration surface, answer:
1. What user outcome needs it?
2. What is the simplest alternative, and why isn't it enough?
3. Who maintains it, and what breaks if we delete it later?

If the answers are weak, leave it out and list it under "Deliberately not built".

## Parity without bloat

Parity is measured by what users can accomplish, not by matching every competitor screen. When a competitor feature can be met by a simpler design (for example, an approval queue instead of a configurable workflow engine), choose the simpler design and note the trade-off.
