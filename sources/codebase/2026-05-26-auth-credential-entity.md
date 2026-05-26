---
title: "Source: AuthCredential Domain Entity"
tags: [source, auth, domain, entity, ddd]
source: raw/codebase/auth-credential-entity.rs
date: 2026-05-26
status: active
---

# Source: AuthCredential Domain Entity

## Goal

Aggregate root for authentication in auth-service. Pure domain logic, no I/O.

## Key Facts Extracted

- Fields: id, user_id, email (Email VO), password_hash (PasswordHash VO), email_verified, verification token/expiry, failed_login_attempts, locked_until, last_login info, account_status (AccountStatus VO), system_role (SystemRole VO), password reset fields, timestamps
- Construction: `new()` for registration (defaults: active, user role, unverified), `from_persisted()` for DB reconstruction
- Value objects enforce invariants at construction: `Email`, `PasswordHash`, `AccountStatus`, `SystemRole`
- Account lockout via `failed_login_attempts` and `locked_until`
- Soft delete via `deleted_at`

## Entities Referenced

- [[auth-credential-entity]], [[auth-service]]

## Concepts Referenced

- [[domain-driven-design]], [[aggregate-root]]

## Sources

- `raw/codebase/auth-credential-entity.rs`

## Related

- [[2026-05-26-auth-application-service]]
- [[2026-05-26-jwt-manager]]
