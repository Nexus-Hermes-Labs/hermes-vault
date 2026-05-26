---
title: NATS JetStream
tags: [entity, infrastructure, messaging, events]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# NATS JetStream

Async event bus for cross-service domain events with at-least-once delivery.

- **Port**: 4222 (client), 8222 (monitoring)
- **Container**: hermes-nats
- **Version**: 2.10

## Streams

| Stream | Subjects | Producer |
|---|---|---|
| AUTH_EVENTS | `auth.>` | [[auth-service]] |
| CHAT_EVENTS | `chat.>` | [[chat-service]] |
| MESSAGING_EVENTS | `messaging.>` | [[messaging-service]] |

## Key Characteristics

- File-backed storage, 7-day max_age, 2-hour duplicate_window
- Publishers use [[transactional-outbox]] (not inline publish)
- Durable consumers: idempotent via `processed_events` table
- Ephemeral consumers: for real-time fanout ([[realtime-service]])

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-outbox-module]]

## Related

- [[transactional-outbox]], [[event-envelope]]
