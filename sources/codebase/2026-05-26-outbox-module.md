---
title: "Source: Outbox Module — Transactional Event Publishing"
tags: [source, outbox, nats, events, infrastructure]
source: raw/codebase/outbox-mod.rs
date: 2026-05-26
status: active
---

# Source: Outbox Module — Transactional Event Publishing

## Goal

Shared infrastructure for the Transactional Outbox pattern used across auth, chat, and messaging services.

## Key Facts Extracted

- Module re-exports: `JetStreamConsumerRunner`, `JetStreamEventHandler`, `ephemeral_fanout_consumer`, `OutboxPublisherTask`, `OutboxRepository`, `OutboxStreamConfig`, `ensure_stream`, `OutboxWriter`, `PgOutboxWriter`, `SharedTx`, `NewOutboxEvent`
- Sub-modules: consumer, ephemeral, publisher, repository, stream, types, writer
- `SharedTx = Arc<Mutex<Option<Transaction>>>` enables multiple writers to share one transaction
- `new_shared_tx` and `tx_consumed_err` are helper functions for transaction lifecycle
- `AggregateType` and `OutboxEventType` provide type-safe event classification

## Concepts Referenced

- [[transactional-outbox]], [[event-envelope]]

## Entities Referenced

- [[outbox-module]]

## Sources

- `raw/codebase/outbox-mod.rs`

## Related

- [[2026-05-26-auth-application-service]]
- [[decision-transactional-outbox]]
- [[nats-jetstream]]
