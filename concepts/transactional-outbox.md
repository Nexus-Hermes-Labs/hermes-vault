---
title: Transactional Outbox Pattern
tags: [concept, events, consistency, nats]
source: raw/docs/PATTERNS.md
date: 2026-05-26
status: active
---

# Transactional Outbox Pattern

Guarantees atomic consistency between database writes and event publishing. The event is written to an `outbox_events` table in the **same transaction** as the aggregate write. A background worker drains the outbox to [[nats-jetstream]].

## Why Not Inline Publish

1. DB commits, NATS fails → consumer never sees event
2. NATS succeeds, DB rolls back → phantom event
3. Process crash between commit and publish → lost event

The outbox eliminates all three.

## Components

See [[outbox-module]] for the shared implementation in `common::infrastructure::outbox`.

## Services Using Outbox

- [[auth-service]] (publisher)
- [[chat-service]] (publisher)
- [[messaging-service]] (publisher)
- [[user-service]] (durable consumer)
- [[realtime-service]] (ephemeral consumer)

## Sources

- [[2026-05-26-patterns-doc]]
- [[2026-05-26-outbox-module]]

## Related

- [[decision-transactional-outbox]], [[event-envelope]], [[nats-jetstream]]
