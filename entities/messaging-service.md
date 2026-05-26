---
title: messaging-service
tags: [entity, service, messaging]
source: raw/codebase/traefik-routes.yml
date: 2026-05-26
status: active
---

# messaging-service

Message delivery orchestration and DM/conversation management. Handles reactions.

- **HTTP port**: 8084 (per Traefik routes.yml)
- **Status**: In progress
- **Route**: `/api/v1/conversations`

## Key Characteristics

- Publishes via [[transactional-outbox]]: `messaging.reaction.added`, `messaging.reaction.removed`
- Full DDD structure in codebase
- Not yet documented in ARCHITECTURE.md or ROADMAP.md (added to both on 2026-05-25)

## Sources

- [[2026-05-26-traefik-routes]]
- [[2026-05-26-roadmap-doc]]
- [[2026-05-26-messaging-message-entity]]

## Related

- [[chat-service]], [[realtime-service]]
- [[messaging-message-entity]]
