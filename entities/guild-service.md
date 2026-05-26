---
title: guild-service
tags: [entity, service, guilds]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# guild-service

Manages guilds (servers), roles with bitflag-based permissions, member management, and invite links.

- **HTTP port**: 8086
- **gRPC port**: 50056
- **Status**: Complete

## Key Characteristics

- Aggregate root: [[guild-entity]]
- Bitflag permission system (i64 with grant/revoke/has methods)
- Invite system with expiry and usage limits
- gRPC client → [[channel-service]] for default channels on guild creation
- gRPC client → [[user-service]] for profile lookups
- gRPC server: `GetGuild` (called by [[channel-service]])

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-guild-entity]]

## Related

- [[channel-service]], [[user-service]]
