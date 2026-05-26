---
title: PasswordHistoryEntry Entity
tags: [entity, domain, auth, password]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# PasswordHistoryEntry Entity

Domain entity in [[auth-service]] that tracks previous password hashes to prevent reuse.

## Fields

- id (UUID)
- credential_id (UUID, FK to auth_credentials)
- password_hash (TEXT, Argon2id hash)
- created_at (TIMESTAMPTZ)

## Behavior

- On password change or reset, the old hash is recorded
- Before accepting a new password, the last N hashes (configured by [[password-policy]]) are checked via Argon2 verify
- Repository trait: `find_recent(credential_id, limit)`, `record(credential_id, hash)`

## Database

Table: `password_history` in auth-service database. Index on `(credential_id, created_at DESC)` for efficient recent-history queries.

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[auth-credential-entity]]
- [[password-policy]]
- [[decision-argon2-vs-sha256]]
