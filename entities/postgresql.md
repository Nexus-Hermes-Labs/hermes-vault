---
title: PostgreSQL
tags: [entity, infrastructure, database]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# PostgreSQL

Primary data store — PostgreSQL 16, single shared instance with per-service databases.

- **Port**: 5432
- **Container**: hermes-postgres

## Key Characteristics

- Per-service databases: hermes_auth, hermes_user, hermes_guild, hermes_channel, hermes_chat, etc.
- No cross-service foreign keys (bounded contexts at storage layer)
- SQLx compile-time query verification via `query!()` / `query_as!()`
- Migrations per-service in `services/{name}/migrations/`
- Post-MVP plan: database-per-service with isolated instances

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-infra-docker-compose]]

## Related

- [[decision-shared-database]], [[unit-of-work]]
