---
title: "Source: ARCHITECTURE.md — System Architecture Reference"
tags: [source, architecture, services, networking]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# Source: ARCHITECTURE.md — System Architecture Reference

## Goal

Comprehensive system architecture document covering all services, infrastructure, communication patterns, authentication flow, observability, and scaling strategy.

## Key Facts Extracted

- 13 backend services in a Rust Cargo workspace (6 active, 7 stubbed)
- Single Traefik edge proxy handles all external HTTP traffic
- ForwardAuth middleware delegates JWT validation to [[auth-service]]
- gRPC for synchronous service-to-service calls (4 active connections)
- NATS JetStream for async domain events via [[transactional-outbox]] pattern
- Single shared PostgreSQL instance (MVP trade-off), per-service databases
- [[redis]] for sessions, caching, presence
- Full observability stack: Loki (logs), Prometheus (metrics), Tempo (traces), Grafana (dashboards)

## Decisions Found

- [[decision-shared-database]] — single Postgres instance for MVP simplicity
- [[decision-traefik-edge-proxy]] — Traefik over custom API gateway
- [[decision-forward-auth]] — centralized JWT at edge, no parsing in downstream services

## Entities Referenced

- [[auth-service]], [[user-service]], [[guild-service]], [[channel-service]], [[chat-service]], [[messaging-service]], [[realtime-service]]
- [[traefik]], [[postgresql]], [[redis]], [[nats-jetstream]]
- [[grafana]], [[prometheus]], [[loki]], [[tempo]]

## Sources

- `raw/docs/ARCHITECTURE.md`

## Related

- [[2026-05-26-technologies-doc]]
- [[2026-05-26-patterns-doc]]
- [[microservices-architecture]]
- [[gateway-authentication]]
