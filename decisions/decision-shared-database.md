---
title: "Decision: Shared PostgreSQL Instance (MVP)"
tags: [decision, database, architecture]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# Decision: Shared PostgreSQL Instance (MVP)

**Status**: Accepted (MVP trade-off)

All services share one PostgreSQL instance with separate databases per service. No cross-service foreign keys. Simpler for MVP — no distributed transactions, ACID guarantees, easy local development. Post-MVP plan: database-per-service with fully isolated instances.

## Sources

- [[2026-05-26-architecture-doc]]

## Related

- [[postgresql]]
