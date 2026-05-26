---
title: Domain-Driven Design (DDD)
tags: [concept, architecture, ddd]
source: raw/docs/PATTERNS.md
date: 2026-05-26
status: active
---

# Domain-Driven Design (DDD)

Every Hermes backend service follows a 6-layer hexagonal architecture where dependencies only flow inward.

## Layers

1. **domain/** — Pure business logic, entities, value objects, repository traits. No external dependencies.
2. **application/** — Use-case orchestration, calls domain + infrastructure ports.
3. **infrastructure/** — Implements domain ports (SQLx repos, gRPC clients, NATS publishers, outbox).
4. **presentation/** — HTTP handlers (Axum) and gRPC servers (Tonic). DTOs in/out.
5. **state/** — AppState composition (groups services into named sub-states).
6. **bootstrap/** — Startup wiring via AppBuilder, server initialization.

## Related Concepts

- [[hexagonal-architecture]], [[aggregate-root]], [[repository-pattern]], [[unit-of-work]]

## Sources

- [[2026-05-26-patterns-doc]]

## Related

- [[auth-service]], [[guild-service]]
