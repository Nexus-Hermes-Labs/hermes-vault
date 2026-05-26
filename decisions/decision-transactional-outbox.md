---
title: "Decision: Transactional Outbox for Event Publishing"
tags: [decision, events, consistency]
source: raw/docs/PATTERNS.md
date: 2026-05-26
status: active
---

# Decision: Transactional Outbox for Event Publishing

**Status**: Accepted (May 2026)

Inline NATS publish after DB commit had three failure modes (lost events, phantom events, crash gaps). Solution: write events to `outbox_events` in the same transaction as the aggregate. Background worker drains to JetStream.

Migrated: [[auth-service]], [[chat-service]], [[messaging-service]].

## Sources

- [[2026-05-26-patterns-doc]]
- [[2026-05-26-outbox-module]]

## Related

- [[transactional-outbox]], [[outbox-module]], [[nats-jetstream]]
