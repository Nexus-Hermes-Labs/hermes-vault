---
title: auth-service
tags: [entity, service, authentication]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# auth-service

Backend service handling all authentication: registration, login/logout, token management, email verification, and the ForwardAuth endpoint.

- **HTTP port**: 8081
- **gRPC port**: 50051
- **Status**: Complete
- **Codebase**: `hermes-be/services/auth-service/`

## Key Characteristics

- Aggregate root: [[auth-credential-entity]]
- Passwords hashed with Argon2id, tokens hashed with SHA-256
- JWT HS256 with separate access/refresh secrets
- Publishes events via [[transactional-outbox]] to `AUTH_EVENTS` stream
- gRPC client → [[user-service]] for profile creation on registration
- Background tasks: email verification cleanup (hourly), outbox publisher

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-auth-application-service]]
- [[2026-05-26-auth-credential-entity]]

## Related

- [[jwt-manager]], [[traefik]], [[gateway-authentication]]
