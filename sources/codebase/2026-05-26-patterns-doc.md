---
title: "Source: PATTERNS.md — 19 Canonical Code Patterns"
tags: [source, patterns, ddd, architecture]
source: raw/docs/PATTERNS.md
date: 2026-05-26
status: active
---

# Source: PATTERNS.md — 19 Canonical Code Patterns

## Goal

Reference document for all code patterns used across every Hermes backend service.

## Key Facts Extracted

- 6-layer hexagonal architecture: domain → application → infrastructure → presentation → state → bootstrap
- Manual DI via AppBuilder (no framework)
- Generic `Repository<T, ID>` base trait with domain-specific extensions
- Unit of Work with `SharedTx = Arc<Mutex<Option<Transaction>>>` for concurrent writer access
- Aggregate roots with invariant-enforcing value objects
- `EventEnvelope<T>` wraps domain events with metadata (event_id, correlation_id, etc.)
- Hierarchical errors: DomainError → ApplicationError → ApiError with `From` conversions
- `RequestUser` extractor reads Traefik-injected headers
- `OnceCell` config singleton loaded once at startup
- Transactional Outbox + idempotent consumer for event publishing

## Concepts Extracted

- [[domain-driven-design]]
- [[hexagonal-architecture]]
- [[unit-of-work]]
- [[repository-pattern]]
- [[transactional-outbox]]
- [[gateway-authentication]]
- [[event-envelope]]
- [[graceful-shutdown]]

## Sources

- `raw/docs/PATTERNS.md`

## Related

- [[2026-05-26-architecture-doc]]
- [[auth-service]]
