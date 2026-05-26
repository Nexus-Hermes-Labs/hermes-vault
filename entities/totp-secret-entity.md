---
title: TotpSecret Entity
tags: [entity, domain, auth, mfa, totp]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# TotpSecret Entity

Domain entity in [[auth-service]] that stores TOTP secrets for MFA authentication.

## Fields

- id (UUID)
- credential_id (UUID, FK to auth_credentials)
- secret_encrypted (BYTEA, AES-256-GCM encrypted)
- enabled (BOOLEAN, false until confirmed with valid code)
- created_at (TIMESTAMPTZ)

## Behavior

- Setup flow: generate secret → store as pending (enabled=false) → user confirms with valid TOTP code → activate (enabled=true)
- Login flow: after password check, if enabled TOTP exists → issue short-lived MFA JWT (typ="mfa", 5 min) → user submits code via `/mfa/challenge` → issue real tokens
- Encryption: AES-256-GCM with single `totp_encryption_key` from auth-service config
- Repository trait: `find_enabled(credential_id)`, `upsert_pending(...)`, `activate(id)`, `delete(credential_id)`

## Dependencies

- `totp-lite = "2"` for TOTP generation/verification
- `base32 = "0.5"` for secret encoding
- `aes-gcm = "0.10"` for encryption

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[auth-credential-entity]]
- [[mfa-backup-code-entity]]
- [[jwt-manager]]
