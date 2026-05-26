---
title: AuditLog Entity
tags: [entity, domain, auth, audit, security]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# AuditLog Entity

Domain entity in [[auth-service]] for structured security audit logging.

## Fields

- id (UUID)
- credential_id (UUID, nullable for pre-auth events)
- event_type (TEXT, e.g. "LoginSuccess", "MfaEnabled")
- ip_address (INET)
- user_agent (TEXT)
- metadata (JSONB, event-specific data)
- created_at (TIMESTAMPTZ)

## Event Types

`LoginSuccess`, `LoginFailed`, `PasswordChanged`, `PasswordResetRequested`, `PasswordResetCompleted`, `MfaEnabled`, `MfaDisabled`, `MfaChallengeSuccess`, `MfaChallengeFailed`, `WebAuthnRegistered`, `WebAuthnAuthenticated`, `OAuthLinked`, `OAuthLogin`, `AccountLocked`, `AccountSuspended`, `AccountDeleted`, `SessionRevoked`

## Write Modes

- **Sync** (`record`): inside UoW transaction for security-critical events (login, password change, MFA changes). Guaranteed consistency.
- **Best-effort** (`record_best_effort`): spawned Tokio task for informational events. Fire-and-forget.

## Storage

Stored in auth-service database. [[admin-service]] reads via gRPC `GetAuditLogs` RPC for the admin dashboard.

## Indexes

- `(credential_id, created_at DESC)` — per-user audit trail
- `(event_type, created_at DESC)` — event-type filtering

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[auth-service]]
- [[admin-service]]
- [[decision-audit-log-placement]]
