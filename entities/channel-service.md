---
title: channel-service
tags: [entity, service, channels]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# channel-service

Manages text and voice channels, categories, and channel-level permission overrides.

- **HTTP port**: 8083
- **gRPC port**: 50053
- **Status**: Complete

## Key Characteristics

- gRPC client → [[guild-service]] for ownership/permission verification
- gRPC server: `CreateDefaultChannels` (called by [[guild-service]])
- Channel types: text, voice
- Permission overrides per-role and per-user

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-channel-entity]]

## Related

- [[guild-service]], [[chat-service]]
- [[channel-entity]]
- [[messaging-message-entity]]
