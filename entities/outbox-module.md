---
title: Outbox Module
tags: [entity, infrastructure, outbox, events]
source: raw/codebase/outbox-mod.rs
date: 2026-05-26
status: active
---

# Outbox Module

Shared infrastructure for the [[transactional-outbox]] pattern.

- **Location**: `services/common/src/infrastructure/outbox/`
- **Sub-modules**: consumer, ephemeral, publisher, repository, stream, types, writer

## Key Exports

- `OutboxWriter` / `PgOutboxWriter` — writes to outbox_events table within UoW
- `OutboxPublisherTask` — background task draining outbox to JetStream
- `OutboxRepository` — fetches publishable events with exponential backoff
- `JetStreamConsumerRunner` / `JetStreamEventHandler` — idempotent durable consumer
- `ephemeral_fanout_consumer` — for real-time fanout
- `SharedTx` — `Arc<Mutex<Option<Transaction>>>` for concurrent writer access

## Sources

- [[2026-05-26-outbox-module]]

## Related

- [[nats-jetstream]], [[decision-transactional-outbox]], [[unit-of-work]]
