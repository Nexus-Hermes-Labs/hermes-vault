# Transactional Outbox Pattern

## Problem

Direct `nats.publish` after `repo.save` has three failure modes:
1. DB commits, NATS fails → consumer never sees the event
2. NATS succeeds, DB rolls back → phantom event for a nonexistent row
3. Process crashes between commit and publish → same as (1)

## Solution

Write the domain event to an `outbox_events` table in the **same transaction** as the aggregate write. A separate background worker drains the outbox to JetStream.

## Architecture

```
Application Service
    │
    ├── uow.messages().save(&message)     ─┐
    └── uow.outbox().save(&outbox_event)   ├── same SQLx transaction
                                            ─┘
                                            
OutboxPublisherTask (background, every few seconds)
    │
    ├── SELECT pending/failed events WHERE next_retry_at <= NOW()
    ├── nats.publish(subject, payload, Nats-Msg-Id: event_id)
    ├── UPDATE status = 'published', published_at = NOW()
    └── On failure: retry_count++, next_retry_at += POWER(2, retry_count) (capped at 1h)
```

## Shared Components (`common::infrastructure::outbox`)

| Component | Role |
|---|---|
| `OutboxWriter` / `PgOutboxWriter` | Writes to `outbox_events` within a UoW transaction |
| `OutboxRepository` | Reads publishable events, applies exponential backoff on failure |
| `OutboxPublisherTask` | Background task: drain outbox → JetStream |
| `OutboxStreamConfig` + `ensure_stream` | Shared stream config (retention, max-age, dedup window) |
| `JetStreamEventHandler` | Trait for idempotent event handling |
| `JetStreamConsumerRunner` | Runs handler with dedup via `processed_events` table |
| `ephemeral_fanout_consumer` | For real-time fanout where durability isn't needed |

## Database Schema

Every publishing service needs `outbox_events`:

```sql
CREATE TABLE outbox_events (
    id UUID PRIMARY KEY,
    aggregate_id UUID NOT NULL,
    aggregate_type TEXT NOT NULL,
    event_type TEXT NOT NULL,         -- doubles as NATS subject
    payload JSONB NOT NULL,
    source_service TEXT NOT NULL,
    status TEXT NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'published', 'failed')),
    retry_count INTEGER NOT NULL DEFAULT 0,
    last_error TEXT,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    next_retry_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    published_at TIMESTAMPTZ
);
```

Every consuming service needs `processed_events`:

```sql
CREATE TABLE processed_events (
    event_id UUID PRIMARY KEY,
    event_type TEXT NOT NULL,
    processed_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

## Services Using Outbox

- auth-service (publisher)
- chat-service (publisher)
- messaging-service (publisher)
- user-service (durable consumer)
- realtime-service (ephemeral consumer)
