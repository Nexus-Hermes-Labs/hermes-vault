---
title: Event Envelope
tags: [concept, events, ddd]
source: raw/docs/PATTERNS.md
date: 2026-05-26
status: active
---

# Event Envelope

`EventEnvelope<T: DomainEvent>` wraps domain events with metadata alongside the payload.

## Fields

- `event_id` (Uuid) — unique per event, used for deduplication
- `event_type` (String) — doubles as NATS subject (e.g., `auth.user.registered`)
- `aggregate_id` (Uuid)
- `version` (u32)
- `occurred_at` (DateTime)
- `source_service` (String)
- `correlation_id` (Option<Uuid>) — for distributed tracing
- `payload` (T)

## Sources

- [[2026-05-26-patterns-doc]]

## Related

- [[transactional-outbox]], [[nats-jetstream]]
