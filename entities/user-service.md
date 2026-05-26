---
title: user-service
tags: [entity, service, users]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# user-service

Manages user profiles, relationships (friends/blocks), privacy settings, badges, and discriminators.

- **HTTP port**: 8082
- **gRPC port**: 50052
- **Status**: Complete

## Key Characteristics

- gRPC server: `GetUserProfile` (called by [[guild-service]])
- gRPC server: `CreateUserProfile` (called by [[auth-service]] on registration)
- Durable NATS consumer: materializes profiles from `user.created` events
- Bidirectional friendship sync via PostgreSQL triggers

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-user-profile-entity]]

## Related

- [[auth-service]], [[guild-service]]
- [[user-profile-entity]]
