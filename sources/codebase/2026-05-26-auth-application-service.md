---
title: "Source: AuthService Application Layer"
tags: [source, auth, application-service, authentication]
source: raw/codebase/auth-service-application.rs
date: 2026-05-26
status: active
---

# Source: AuthService Application Layer

## Goal

Main authentication orchestrator — coordinates registration, login, logout, token refresh, and email verification.

## Key Facts Extracted

- Access token expiry: 6 hours, refresh token: 30 days, verification token: 24 hours
- Dependencies injected via `Arc<dyn Trait>`: credential repo, session repo, UoW factory, password service, token hasher, JWT manager, email service
- Uses [[transactional-outbox]]: writes `UserCreatedEvent` to outbox in same transaction as credential save
- Unique email constraint enforced by Postgres error code `23505` on `auth_credentials_email_key`
- Registration flow: validate email → hash password (Argon2) → create credential → save via UoW (credential + outbox event) → send verification email → generate tokens

## Entities Referenced

- [[auth-service]], [[auth-credential-entity]], [[jwt-manager]]

## Concepts Referenced

- [[unit-of-work]], [[transactional-outbox]], [[event-envelope]]

## Sources

- `raw/codebase/auth-service-application.rs`

## Related

- [[2026-05-26-auth-credential-entity]]
- [[2026-05-26-jwt-manager]]
- [[gateway-authentication]]
