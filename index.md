---
title: Hermes Vault — Index
tags: [index, meta]
status: active
date: 2026-05-26
---

# Hermes Vault — Index

Central knowledge archive for the Hermes real-time communication platform.

## Sources

- [[2026-05-26-architecture-doc]] — ARCHITECTURE.md ingest
- [[2026-05-26-technologies-doc]] — TECHNOLOGIES.md ingest
- [[2026-05-26-patterns-doc]] — PATTERNS.md ingest
- [[2026-05-26-roadmap-doc]] — ROADMAP.md ingest
- [[2026-05-26-auth-application-service]] — auth authentication service.rs
- [[2026-05-26-auth-credential-entity]] — auth credential entity.rs
- [[2026-05-26-guild-entity]] — guild entity.rs
- [[2026-05-26-jwt-manager]] — JWT manager module
- [[2026-05-26-outbox-module]] — outbox infrastructure module
- [[2026-05-26-request-context]] — request context / observability
- [[2026-05-26-traefik-routes]] — Traefik dynamic routes.yml
- [[2026-05-26-infra-docker-compose]] — Docker Compose infrastructure
- [[2026-05-26-channel-entity]] — channel domain entity.rs
- [[2026-05-26-messaging-message-entity]] — messaging message entity.rs
- [[2026-05-26-user-profile-entity]] — user profile entity.rs
- [[2026-05-26-chat-message-entity]] — chat message entity.rs
- [[2026-05-26-auth-enhancement-plan]] — auth enhancement + admin service plan

## Entities

### Services
- [[auth-service]] — authentication, JWT, sessions, MFA, OAuth, WebAuthn (port 8081)
- [[user-service]] — profiles, relationships, privacy (port 8082)
- [[guild-service]] — guilds, roles, members (port 8083)
- [[channel-service]] — channels, categories (port 8083)
- [[chat-service]] — guild channel messages, reactions (port 8094)
- [[messaging-service]] — DMs, conversations (port 8084)
- [[realtime-service]] — WebSocket gateway (planned)
- [[admin-service]] — system-level RBAC, user management (port 8095, planned)

### Domain Aggregates
- [[auth-credential-entity]] — auth credential aggregate root
- [[user-profile-entity]] — user profile aggregate root
- [[guild-entity]] — guild aggregate root
- [[channel-entity]] — channel aggregate root
- [[chat-message-entity]] — chat message aggregate root
- [[messaging-message-entity]] — messaging message aggregate root

### Auth Enhancement Entities (planned)
- [[password-history-entity]] — password reuse prevention
- [[totp-secret-entity]] — TOTP MFA secrets
- [[mfa-backup-code-entity]] — MFA recovery codes
- [[webauthn-credential-entity]] — FIDO2 passkeys
- [[oauth-account-entity]] — external OAuth provider links
- [[audit-log-entity]] — security event audit trail
- [[rate-limit-bucket-entity]] — per-endpoint rate limiting

### Admin Service Entities (planned)
- [[admin-role-entity]] — system-level roles
- [[admin-permission-entity]] — granular permissions

### Infrastructure
- [[traefik]] — edge reverse proxy with ForwardAuth
- [[postgresql]] — per-service schemas, SQLx compile-time queries
- [[redis]] — caching, sessions, presence
- [[nats-jetstream]] — durable event streaming
- [[outbox-module]] — shared transactional outbox infrastructure
- [[jwt-manager]] — JWT creation and validation
- [[request-context-module]] — distributed tracing / observability context

## Concepts

- [[aggregate-root]] — DDD aggregate root pattern
- [[domain-driven-design]] — bounded contexts, ubiquitous language
- [[hexagonal-architecture]] — ports & adapters (6-layer variant)
- [[microservices-architecture]] — service decomposition approach
- [[repository-pattern]] — data access abstraction
- [[unit-of-work]] — transaction management
- [[transactional-outbox]] — reliable event publishing
- [[event-envelope]] — standard event wrapper format
- [[gateway-authentication]] — ForwardAuth pattern
- [[distributed-tracing]] — request correlation across services
- [[observability-stack]] — logging, metrics, tracing
- [[graceful-shutdown]] — clean service termination
- [[password-policy]] — config-based password validation rules
- [[rate-limiting]] — two-tier rate limiting (Traefik + auth-service)
- [[multi-factor-authentication]] — TOTP, backup codes, WebAuthn

## Decisions

- [[decision-traefik-edge-proxy]] — Traefik over custom API gateway
- [[decision-forward-auth]] — ForwardAuth over per-service JWT parsing
- [[decision-argon2-vs-sha256]] — Argon2id for password hashing
- [[decision-transactional-outbox]] — outbox over direct NATS publish
- [[decision-shared-database]] — per-service schemas in shared Postgres
- [[decision-consul-rolled-back]] — Consul built then rolled back
- [[decision-mfa-separate-aggregate]] — MFA as separate aggregates, not AuthCredential extension
- [[decision-password-policy-config]] — config-based policy, not DB-stored
- [[decision-rate-limiting-pg]] — PG fixed-window inside auth-service
- [[decision-admin-service-separation]] — separate admin-service for system-level RBAC
- [[decision-audit-log-placement]] — audit logs in auth-service DB
- [[decision-oauth-csrf-stateless]] — stateless HMAC-based CSRF for OAuth

## Issues

- [[issue-port-discrepancy]] — port conflict between docs and Traefik routes
- [[issue-chat-endpoints-incomplete]] — chat-service HTTP endpoints partial
- [[issue-no-grpc-integration-tests]] — gRPC calls lack integration test coverage
- [[issue-realtime-incomplete]] — realtime-service not yet implemented

## Syntheses

- [[service-architecture-overview]] — service landscape, communication, cross-service flows
- [[security-and-authentication]] — auth flow, ForwardAuth, password security, tokens
- [[event-driven-architecture]] — outbox pattern, NATS streams, event contracts
- [[domain-model-overview]] — aggregate roots, shared patterns, entity relationships
