---
title: Hermes Vault — Activity Log
tags: [log, meta]
status: active
date: 2026-05-26
---

# Hermes Vault — Activity Log

Chronological record of every ingest, update, and synthesis operation.

## [2026-05-26] setup | vault-initialization

Initial vault setup: directory structure, CLAUDE.md rulebook, index.md, log.md skeleton.

- Created: `raw/codebase/`, `raw/docs/`, `sources/codebase/`, `entities/`, `concepts/`, `decisions/`, `issues/`, `syntheses/`, `archive/`
- Created: `CLAUDE.md`, `index.md`, `log.md`

## [2026-05-26] ingest | architecture-doc

- Created: `sources/codebase/2026-05-26-architecture-doc.md`
- Created/Updated: `entities/auth-service.md`, `entities/user-service.md`, `entities/guild-service.md`, `entities/channel-service.md`, `entities/chat-service.md`, `entities/realtime-service.md`
- Created: `concepts/microservices-architecture.md`, `concepts/hexagonal-architecture.md`
- Symlink: `raw/docs/ARCHITECTURE.md`

## [2026-05-26] ingest | technologies-doc

- Created: `sources/codebase/2026-05-26-technologies-doc.md`
- Created/Updated: `entities/postgresql.md`, `entities/redis.md`, `entities/nats-jetstream.md`, `entities/traefik.md`
- Created: `concepts/observability-stack.md`
- Symlink: `raw/docs/TECHNOLOGIES.md`

## [2026-05-26] ingest | patterns-doc

- Created: `sources/codebase/2026-05-26-patterns-doc.md`
- Created: `concepts/domain-driven-design.md`, `concepts/aggregate-root.md`, `concepts/repository-pattern.md`, `concepts/unit-of-work.md`, `concepts/graceful-shutdown.md`
- Symlink: `raw/docs/PATTERNS.md`

## [2026-05-26] ingest | roadmap-doc

- Created: `sources/codebase/2026-05-26-roadmap-doc.md`
- Updated: `entities/messaging-service.md`
- Symlink: `raw/docs/ROADMAP.md`

## [2026-05-26] ingest | auth-credential-entity

- Created: `sources/codebase/2026-05-26-auth-credential-entity.md`
- Created: `entities/auth-credential-entity.md`
- Symlink: `raw/codebase/auth-credential-entity.rs`

## [2026-05-26] ingest | auth-application-service

- Created: `sources/codebase/2026-05-26-auth-application-service.md`
- Updated: `entities/auth-service.md`
- Created: `concepts/gateway-authentication.md`
- Symlink: `raw/codebase/auth-service-application.rs`

## [2026-05-26] ingest | guild-entity

- Created: `sources/codebase/2026-05-26-guild-entity.md`
- Created: `entities/guild-entity.md`
- Updated: `entities/guild-service.md`
- Symlink: `raw/codebase/guild-entity.rs`

## [2026-05-26] ingest | jwt-manager

- Created: `sources/codebase/2026-05-26-jwt-manager.md`
- Created: `entities/jwt-manager.md`
- Symlink: `raw/codebase/jwt-manager.rs`

## [2026-05-26] ingest | outbox-module

- Created: `sources/codebase/2026-05-26-outbox-module.md`
- Created: `entities/outbox-module.md`
- Created: `concepts/transactional-outbox.md`, `concepts/event-envelope.md`
- Created: `decisions/decision-transactional-outbox.md`
- Symlink: `raw/codebase/outbox-mod.rs`

## [2026-05-26] ingest | request-context

- Created: `sources/codebase/2026-05-26-request-context.md`
- Created: `entities/request-context-module.md`
- Created: `concepts/distributed-tracing.md`
- Symlink: `raw/codebase/request-context.rs`

## [2026-05-26] ingest | traefik-routes

- Created: `sources/codebase/2026-05-26-traefik-routes.md`
- Updated: `entities/traefik.md`, `entities/chat-service.md`, `entities/messaging-service.md`
- Created: `decisions/decision-forward-auth.md`, `decisions/decision-traefik-edge-proxy.md`
- Created: `issues/issue-port-discrepancy.md`
- Symlink: `raw/codebase/traefik-routes.yml`

## [2026-05-26] ingest | infra-docker-compose

- Created: `sources/codebase/2026-05-26-infra-docker-compose.md`
- Updated: `entities/postgresql.md`, `entities/redis.md`, `entities/nats-jetstream.md`
- Created: `decisions/decision-shared-database.md`, `decisions/decision-argon2-vs-sha256.md`, `decisions/decision-consul-rolled-back.md`
- Created: `issues/issue-chat-endpoints-incomplete.md`, `issues/issue-no-grpc-integration-tests.md`, `issues/issue-realtime-incomplete.md`
- Symlink: `raw/codebase/infra-docker-compose.yml`

## [2026-05-26] ingest | channel-entity

- Created: `sources/codebase/2026-05-26-channel-entity.md`
- Created: `entities/channel-entity.md`
- Updated: `entities/channel-service.md`
- Symlink: `raw/codebase/channel-entity.rs`

## [2026-05-26] ingest | messaging-message-entity

- Created: `sources/codebase/2026-05-26-messaging-message-entity.md`
- Created: `entities/messaging-message-entity.md`
- Updated: `entities/messaging-service.md`
- Symlink: `raw/codebase/messaging-message-entity.rs`

## [2026-05-26] ingest | user-profile-entity

- Created: `sources/codebase/2026-05-26-user-profile-entity.md`
- Created: `entities/user-profile-entity.md`
- Updated: `entities/user-service.md`
- Symlink: `raw/codebase/user-profile-entity.rs`

## [2026-05-26] ingest | chat-message-entity

- Created: `sources/codebase/2026-05-26-chat-message-entity.md`
- Created: `entities/chat-message-entity.md`
- Updated: `entities/chat-service.md`
- Symlink: `raw/codebase/chat-message-entity.rs`

## [2026-05-26] synthesis | all

- Created: `syntheses/service-architecture-overview.md`
- Created: `syntheses/security-and-authentication.md`
- Created: `syntheses/event-driven-architecture.md`
- Created: `syntheses/domain-model-overview.md`
- Updated: `index.md` — all pages indexed by category
- Updated: `log.md` — all ingest entries populated
