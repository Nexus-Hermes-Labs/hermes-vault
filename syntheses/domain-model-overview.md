---
title: Domain Model Overview
tags: [synthesis, domain, ddd, aggregates, entities]
source: multiple
date: 2026-05-26
status: active
---

# Domain Model Overview

Cross-cutting synthesis of Hermes' domain model: aggregate roots, value objects, and patterns shared across bounded contexts.

## Aggregate Roots by Service

| Service | Aggregate Root | Key Value Objects |
|---|---|---|
| [[auth-service]] | [[auth-credential-entity]] | `Email`, `PasswordHash`, `AuthStatus` |
| [[auth-service]] | [[totp-secret-entity]] (planned) | Encrypted TOTP secret |
| [[auth-service]] | [[mfa-backup-code-entity]] (planned) | Hashed backup codes |
| [[auth-service]] | [[webauthn-credential-entity]] (planned) | Public key, sign count |
| [[auth-service]] | [[oauth-account-entity]] (planned) | Provider, provider_user_id |
| [[auth-service]] | [[password-history-entity]] (planned) | Password hash history |
| [[auth-service]] | [[audit-log-entity]] (planned) | Event type, metadata |
| [[user-service]] | [[user-profile-entity]] | `Username`, `UserStatus` |
| [[guild-service]] | [[guild-entity]] | `GuildName`, `MemberRole` |
| [[channel-service]] | [[channel-entity]] | `ChannelName`, `ChannelType` |
| [[chat-service]] | [[chat-message-entity]] | `MessageContent`, `MessageType` |
| [[messaging-service]] | [[messaging-message-entity]] | `MessageContent`, `MessageType`, `MessageTarget` |
| [[admin-service]] (planned) | [[admin-role-entity]] | Role name, system flag |
| [[admin-service]] (planned) | [[admin-permission-entity]] | Permission name |

## Shared Patterns

### Construction
Every aggregate uses a dual-constructor pattern:
- `new()` — creates with validation, generates UUID, sets timestamps
- `from_persisted()` — reconstructs from database row without re-validation

### Soft Delete
All aggregates use `deleted_at: Option<DateTime<Utc>>` instead of hard deletes. The `delete()` method sets the timestamp; `is_active()` / `is_deleted()` query it.

### Partial Update
Update methods accept `Option<T>` fields — only `Some(...)` values are modified. This avoids accidental overwrites.

### Author-Only Operations
Chat and messaging message entities enforce `is_author(requester_id)` before allowing edit or delete operations.

## Cross-Service Entity Relationships

```
auth-credential ──creates──→ user-profile
      │                          │
      ├──has──→ totp-secret      │
      ├──has──→ mfa-backup-code  │
      ├──has──→ webauthn-cred    │
      ├──has──→ oauth-account    │
      ├──has──→ password-history  │
      ├──has──→ audit-log        │
      │                          │
      │  admin-role ──has──→ admin-permission
      │       ↑
      │  user-role (user_id ← role_id)
      │                          │
guild ←──member-of──────────────┘
  │
  └──has──→ channel ←──targets──┐
                                │
              chat-message ─────┘
              messaging-message ──targets──→ channel | conversation
```

## Code Duplication Issue

[[chat-message-entity]] and [[messaging-message-entity]] share ~90% identical code (same fields, same business logic, same validation). No shared trait or base exists. This is a known open thread for potential refactoring.

## DDD Concepts Applied

- [[aggregate-root]] — every entity shown above is an aggregate root
- [[repository-pattern]] — each aggregate has a dedicated repository trait
- [[unit-of-work]] — transaction management across repository operations
- [[hexagonal-architecture]] — ports & adapters in every service
- [[domain-driven-design]] — bounded contexts, ubiquitous language

## Sources

- [[2026-05-26-channel-entity]]
- [[2026-05-26-messaging-message-entity]]
- [[2026-05-26-user-profile-entity]]
- [[2026-05-26-chat-message-entity]]
- [[2026-05-26-auth-credential-entity]]
- [[2026-05-26-guild-entity]]
- [[2026-05-26-patterns-doc]]
- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[service-architecture-overview]]
- [[event-driven-architecture]]
- [[security-and-authentication]]
- [[admin-service]]
- [[decision-mfa-separate-aggregate]]
