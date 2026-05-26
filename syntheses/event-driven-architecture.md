---
title: Event-Driven Architecture
tags: [synthesis, events, nats, outbox, architecture]
source: multiple
date: 2026-05-26
status: active
---

# Event-Driven Architecture

Cross-cutting synthesis of how Hermes services communicate asynchronously through events.

## Core Pattern: Transactional Outbox

All event publishing in Hermes uses the [[transactional-outbox]] pattern:

1. Business operation and outbox record are written in the **same database transaction**
2. Background worker polls the outbox table and publishes to [[nats-jetstream]]
3. Published events are marked as sent (idempotent, at-least-once delivery)

See [[decision-transactional-outbox]] for why this was chosen over direct NATS publish.

## Infrastructure

- **NATS JetStream**: durable message streaming with consumer groups
- **Shared outbox module**: [[outbox-module]] in the common crate, reused by all services
- **Event envelope**: [[event-envelope]] — standard wrapper with metadata (event_id, event_type, timestamp, aggregate_id)

## Event Streams

| Service | Events | Stream |
|---|---|---|
| [[auth-service]] | `user.created`, `user.password_changed` | AUTH_EVENTS |
| [[chat-service]] | `chat.message.created/updated/deleted` | CHAT_EVENTS |
| [[messaging-service]] | `messaging.reaction.added/removed` | MESSAGING_EVENTS |

## Consumer Patterns

- **Durable consumers**: services subscribe with durable names to survive restarts
- **Idempotent processing**: consumers use event_id to deduplicate
- [[user-service]] materializes profiles from `user.created` events

## Services Not Yet Migrated

- [[channel-service]] — no outbox integration yet (channel CRUD events not published)
- [[guild-service]] — guild events not yet published via outbox
- [[realtime-service]] — planned as a consumer-only service (WebSocket push)

## Sources

- [[2026-05-26-outbox-module]]
- [[2026-05-26-architecture-doc]]
- [[2026-05-26-technologies-doc]]

## Related

- [[service-architecture-overview]]
- [[transactional-outbox]]
- [[nats-jetstream]]
- [[outbox-module]]
- [[event-envelope]]
