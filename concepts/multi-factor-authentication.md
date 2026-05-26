---
title: Multi-Factor Authentication (MFA)
tags: [concept, security, authentication, mfa, totp, webauthn]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# Multi-Factor Authentication (MFA)

Second-factor authentication in [[auth-service]] supporting TOTP and WebAuthn/Passkeys.

## TOTP Flow

1. User enables MFA: `POST /mfa/setup` → receives secret + otpauth URI
2. User confirms: `POST /mfa/confirm` with valid 6-digit code → activates TOTP
3. On login: password OK + MFA enabled → short-lived MFA JWT (typ="mfa", 5 min)
4. User submits: `POST /mfa/challenge` with MFA token + TOTP code → real access/refresh tokens

## WebAuthn/Passkey Flow

1. Registration: `begin` → browser creates credential → `complete` → stored in DB
2. Authentication: `begin` → browser signs challenge → `complete` → tokens issued
3. Challenges stored in [[redis]] with 300s TTL

## Backup Codes

10 one-time codes generated when MFA is enabled. Each code is hashed and single-use. If TOTP verification fails during MFA challenge, backup codes are tried as fallback.

## Security Properties

- TOTP secrets encrypted at rest (AES-256-GCM)
- MFA JWT prevents session hijacking (short-lived, single-purpose)
- WebAuthn sign counter prevents authenticator cloning
- Rate limiting on MFA challenge endpoint (5 attempts / 5 min)

## Sources

- [[2026-05-26-auth-enhancement-plan]]
- [[decision-mfa-separate-aggregate]]

## Related

- [[totp-secret-entity]]
- [[mfa-backup-code-entity]]
- [[webauthn-credential-entity]]
- [[jwt-manager]]
- [[auth-service]]
