---
title: Microservices Architecture
tags: [concept, architecture, system-design]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# Microservices Architecture

Hermes uses 13 backend services, each owning its own domain, database schema, and deployment unit. Communication is via HTTP REST (client-facing, through [[traefik]]), gRPC (synchronous service-to-service), and [[nats-jetstream]] (asynchronous events).

All services are stateless — no in-memory session state. Any pod can handle any request.

## Sources

- [[2026-05-26-architecture-doc]]

## Related

- [[domain-driven-design]], [[gateway-authentication]], [[transactional-outbox]]
