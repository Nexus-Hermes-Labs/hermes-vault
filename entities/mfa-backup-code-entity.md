---
title: MfaBackupCode Entity
tags: [entity, domain, auth, mfa]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# MfaBackupCode Entity

Domain entity in [[auth-service]] for one-time MFA backup codes, used when TOTP device is unavailable.

## Fields

- id (UUID)
- credential_id (UUID, FK to auth_credentials)
- code_hash (TEXT, hashed backup code)
- used_at (TIMESTAMPTZ, NULL until consumed)
- created_at (TIMESTAMPTZ)

## Behavior

- 10 codes generated when MFA is enabled via `POST /api/v1/auth/mfa/backup-codes`
- Each code is single-use: consuming sets `used_at`
- On MFA challenge, if TOTP code fails, attempt backup code verification as fallback
- Regenerating codes deletes all existing codes and creates 10 new ones
- Repository trait: `find_unused(credential_id)`, `consume(credential_id, code)`, `delete_all(credential_id)`

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[totp-secret-entity]]
- [[auth-credential-entity]]
