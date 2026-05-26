---
title: Aggregate Root
tags: [concept, ddd, domain-modeling]
source: raw/docs/PATTERNS.md
date: 2026-05-26
status: active
---

# Aggregate Root

DDD pattern where a cluster of domain objects is treated as a single unit. The aggregate root is the only entry point — external code never reaches internal entities directly.

## Hermes Examples

- [[auth-credential-entity]] — root for authentication in auth-service
- [[guild-entity]] — root for guilds, owns roles/members/invites
- Value objects enforce invariants at construction: `Email`, `PasswordHash`, `GuildName`, `GuildVisibility`

## Sources

- [[2026-05-26-patterns-doc]]
- [[2026-05-26-auth-credential-entity]]
- [[2026-05-26-guild-entity]]

## Related

- [[domain-driven-design]]
