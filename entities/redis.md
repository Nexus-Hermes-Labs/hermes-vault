---
title: Redis
tags: [entity, infrastructure, cache, sessions]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# Redis

In-memory key-value store for sessions, caching, and presence.

- **Port**: 6379
- **Container**: hermes-redis
- **Version**: 7

## Current Uses

- Session storage for refresh tokens ([[auth-service]])

## Planned Uses (not yet active)

- User profile cache (1h TTL)
- Relationship cache (5min TTL)
- Presence tracking (live, ephemeral)
- Connection state ([[realtime-service]])
- Rate-limit counters

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-infra-docker-compose]]

## Related

- [[auth-service]], [[postgresql]]
