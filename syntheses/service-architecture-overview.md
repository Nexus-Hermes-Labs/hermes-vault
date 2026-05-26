---
title: Service Architecture Overview
tags: [synthesis, architecture, microservices, overview]
source: multiple
date: 2026-05-26
status: active
---

# Service Architecture Overview

Cross-cutting synthesis of how Hermes services are organized, communicate, and depend on each other.

## Service Landscape

Hermes consists of 7 active services plus 6 stub/planned services:

| Service | HTTP | gRPC | Status | Primary Domain |
|---|---|---|---|---|
| [[auth-service]] | 8081 | 50051 | Complete | Authentication, JWT, sessions |
| [[user-service]] | 8082 | 50052 | Complete | Profiles, relationships, privacy |
| [[guild-service]] | 8083 | — | Complete | Guilds, roles, members |
| [[channel-service]] | 8083 | 50053 | Complete | Channels, categories |
| [[chat-service]] | 8094 | — | ~70% | Guild channel messages, reactions |
| [[messaging-service]] | 8084 | — | In progress | DMs, conversations |
| [[realtime-service]] | 8095 | — | Planned | WebSocket gateway |

## Communication Patterns

1. **Synchronous (gRPC)**: Service-to-service calls for immediate data needs. Example: [[auth-service]] → [[user-service]] `CreateUserProfile` on registration.
2. **Asynchronous (NATS JetStream)**: Event-driven via [[transactional-outbox]]. Events published to durable streams, consumed by interested services.
3. **Edge (HTTP via Traefik)**: All client traffic enters through [[traefik]] reverse proxy with ForwardAuth to [[auth-service]].

## Architectural Principles

- **DDD hexagonal architecture** in every service: domain → application → infrastructure → presentation → state → bootstrap
- **Bounded contexts**: each service owns its data and domain logic
- **Shared infrastructure**: common crate provides [[outbox-module]], [[request-context-module]], [[jwt-manager]], error types
- **No shared database writes**: services read/write only their own schemas (see [[decision-shared-database]])

## Key Cross-Service Flows

### User Registration
1. Client → [[traefik]] → [[auth-service]] HTTP
2. [[auth-service]] creates [[auth-credential-entity]], publishes `user.created` via outbox
3. [[auth-service]] → [[user-service]] gRPC `CreateUserProfile`
4. [[user-service]] creates [[user-profile-entity]]

### Guild Channel Message
1. Client → [[traefik]] → [[chat-service]] HTTP (ForwardAuth passes `X-User-Id`)
2. [[chat-service]] creates [[chat-message-entity]], publishes `chat.message.created` via outbox
3. [[realtime-service]] (planned) subscribes to stream, pushes to WebSocket

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-traefik-routes]]
- [[2026-05-26-infra-docker-compose]]

## Related

- [[security-and-authentication]]
- [[event-driven-architecture]]
- [[domain-model-overview]]
