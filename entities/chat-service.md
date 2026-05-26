---
title: chat-service
tags: [entity, service, messaging]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# chat-service

Handles message CRUD, reactions, attachments, history, and DMs. Publishes to `CHAT_EVENTS` stream.

- **HTTP port**: 8094 (per Traefik routes.yml)
- **Status**: ~70%

## CONTRADICTION

ARCHITECTURE.md and ROADMAP.md list chat-service on port **8084**, but the actual Traefik routes.yml routes to port **8094**. The messaging-service occupies **8084** in routes.yml. See [[2026-05-26-traefik-routes]] for details.

## Key Characteristics

- Publishes via [[transactional-outbox]]: `chat.message.created`, `chat.message.updated`, `chat.message.deleted`
- Remaining work: HTTP endpoints, full NATS publishing, test coverage

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-traefik-routes]]
- [[2026-05-26-chat-message-entity]]

## Related

- [[messaging-service]], [[realtime-service]], [[channel-service]]
- [[chat-message-entity]]
