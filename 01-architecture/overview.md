# Architecture Overview

Hermes is a real-time communication platform with AI-powered translation, built as a Rust microservices system with a React frontend.

## High-Level Architecture

```
                          Browser
                             │
                             ▼
            ┌────────────────────────────────┐
            │  Traefik :80   (edge proxy)     │
            │   ForwardAuth → auth-service    │
            │   Rate limiting: 100 req/s      │
            └────────────────────────────────┘
                             │
      ┌──────────────────────┼──────────────────────┐
      ▼                      ▼                      ▼
┌─────────────┐       ┌─────────────┐        ┌─────────────┐
│ auth :8081  │       │ user :8082  │        │ guild :8086 │  …
│ + gRPC 50051│◄─gRPC─│ + gRPC 50052│        │ + gRPC 50056│
└──────┬──────┘       └──────┬──────┘        └──────┬──────┘
       │                     │                      │
       ▼                     ▼                      ▼
  ┌─────────┐           ┌──────┐              ┌──────────┐
  │Postgres │           │Redis │              │  NATS    │
  │ :5432   │           │:6379 │              │  :4222   │
  └─────────┘           └──────┘              └──────────┘
```

## Design Principles

- **Domain-Driven Design (DDD)**: Every service follows hexagonal architecture with 6 layers
- **Gateway authentication**: JWT validation centralized at Traefik via ForwardAuth — no JWT parsing in downstream services
- **Event-driven**: Cross-service communication via NATS JetStream with Transactional Outbox pattern
- **Compile-time safety**: `unsafe_code` forbidden, `unwrap`/`panic` denied, SQLx compile-time query verification
- **Observability built-in**: Structured logs (Loki), metrics (Prometheus), distributed tracing (Tempo), unified in Grafana

## Communication Patterns

| Pattern | Technology | Use Case |
|---|---|---|
| Client → Service | HTTP REST (via Traefik) | All client-facing APIs |
| Service → Service (sync) | gRPC (Tonic) | Immediate cross-service queries |
| Service → Service (async) | NATS JetStream | Domain events, eventual consistency |
| Client ↔ Server (real-time) | WebSocket | Live message delivery, typing indicators |

## Authentication Flow

```
Client → Traefik → ForwardAuth → auth-service /internal/verify
                                       │
                      200 + X-User-Id, X-User-Role, X-User-Email
                                       │
                                  upstream service (trusts headers)
```

## Current State (May 2026)

- **Phase 1 (MVP Core)**: ~85% complete — auth, user, guild, channel complete; chat and realtime in progress
- **Phase 2-4**: Not started (presence, media, notifications, AI translation, search, voice)
- **Observability**: Fully shipped (logs, metrics, distributed tracing, dashboards)
- **Configuration**: Plain `.env` files (Consul was built and rolled back as over-engineered for pre-prod)
- **Event publishing**: Transactional Outbox pattern across auth, chat, messaging services
