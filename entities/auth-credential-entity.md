---
title: AuthCredential Entity
tags: [entity, domain, auth, aggregate-root]
source: raw/codebase/auth-credential-entity.rs
date: 2026-05-26
status: active
---

# AuthCredential Entity

Aggregate root for authentication in [[auth-service]]. Pure domain logic, no I/O.

## Fields

- id, user_id, email (Email VO), password_hash (PasswordHash VO)
- Email verification: token, expiry, verified flag
- Security: failed_login_attempts, locked_until, last_login
- State: account_status (Active/Suspended/Unverified), system_role (User/Admin)
- Password reset: token, expiry, changed_at
- Timestamps: created_at, updated_at, deleted_at (soft delete)

## Sources

- [[2026-05-26-auth-credential-entity]]

## Related

- [[auth-service]], [[jwt-manager]], [[aggregate-root]]
