# ADR-003: Transactional Outbox for Event Publishing

**Status**: Accepted (May 2026)
**Migrated**: auth-service, chat-service, messaging-service

## Context

Services publishing domain events to NATS inline (after DB commit) had three failure modes:
1. DB commits, NATS fails → lost event
2. NATS succeeds, DB rolls back → phantom event
3. Process crash between commit and publish → lost event

## Decision

Implement the Transactional Outbox pattern: write events to an `outbox_events` table in the same DB transaction as the aggregate write. A background worker drains the outbox to JetStream.

## Design

- Shared building blocks in `common::infrastructure::outbox`
- `OutboxWriter` composed into per-service `UnitOfWork` (same transaction)
- `OutboxPublisherTask` runs as a background task, marks events as published
- Exponential backoff on failure (capped at 1 hour)
- JetStream `Nats-Msg-Id = event_id` for publisher-side dedup
- `processed_events` table for consumer-side idempotency
- Two consumer types: durable (state changes) and ephemeral (real-time fanout)

## Consequences

- Atomic guarantee: DB write and event are committed together or not at all
- Slight latency increase (event delivery not immediate, depends on poll interval)
- Each publishing service needs `outbox_events` table
- Each consuming service needs `processed_events` table
- More infrastructure code, but eliminates entire class of data consistency bugs
