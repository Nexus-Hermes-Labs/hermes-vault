---
title: "Source: Auth Enhancement + Admin Service Implementation Plan"
tags: [source, auth, admin, plan, mfa, oauth, rbac, webauthn]
source: implementation-plan
date: 2026-05-26
status: active
---

# Source: Auth Enhancement + Admin Service Implementation Plan

## Goal

Add 9 capabilities to the Hermes authentication layer and introduce a new admin-service for system-level RBAC.

## Key Facts Extracted

- AuthCredential entity already contains password-reset and account-lockout domain logic (fields: `failed_login_attempts`, `locked_until`, `password_reset_token`, `password_reset_token_expires_at`, `password_changed_at`) — these need HTTP endpoints and application-layer wiring
- Password policy is config-based (not DB-stored) — appropriate for single-tenant deployment
- MFA/TOTP, WebAuthn, OAuth, Password History are separate domain aggregates (not extensions to AuthCredential's 19 fields)
- Rate limiting is per-endpoint PG fixed-window inside auth-service, complementing Traefik's global 100 req/s
- Admin-service on port 8095/50058 (8087 reserved for presence-service Phase 2)
- Admin-service system-level RBAC is distinct from guild-service's per-guild bitflag roles
- Audit logs stored in auth-service DB, exposed to admin-service via gRPC
- TOTP secrets encrypted with AES-256-GCM (single key from config)
- WebAuthn challenges stored in Redis (ephemeral, 300s TTL)
- OAuth CSRF state is stateless: HMAC-SHA256 nonce

## 9 Capabilities

1. **Forgot/Reset Password** — wire existing domain methods to HTTP endpoints
2. **Password Policy** — config-based value object: min/max length, complexity rules
3. **Password History** — new aggregate preventing reuse of last N passwords
4. **Rate Limiting** — PG fixed-window per login/forgot-password/MFA endpoints
5. **MFA/TOTP** — TOTP setup/verify/disable with 10 backup codes
6. **Audit Logging** — structured event log (sync in UoW, best-effort for info)
7. **WebAuthn/Passkeys** — FIDO2 registration/authentication
8. **Google OAuth2** — find-or-create flow with account linking
9. **Admin Service + RBAC** — system roles/permissions, user management via gRPC

## New Domain Aggregates

- [[password-history-entity]] — `id, credential_id, password_hash, created_at`
- [[totp-secret-entity]] — `id, credential_id, secret_encrypted, enabled, created_at`
- [[mfa-backup-code-entity]] — `id, credential_id, code_hash, used_at, created_at`
- [[webauthn-credential-entity]] — `id, credential_id_bytes, user_id, public_key_json, name, sign_count, created_at, last_used_at`
- [[oauth-account-entity]] — `id, credential_id, provider, provider_user_id, email, created_at, updated_at`
- [[audit-log-entity]] — `id, credential_id, event_type, ip_address, user_agent, metadata, created_at`
- [[rate-limit-bucket-entity]] — `key, window_start, count, expires_at`
- [[admin-role-entity]] — `id, name, description, is_system, created_at, updated_at`
- [[admin-permission-entity]] — `id, name, description, created_at`

## Architectural Decisions

- [[decision-mfa-separate-aggregate]] — MFA as separate aggregate, not AuthCredential extension
- [[decision-password-policy-config]] — config-based policy, not DB-stored
- [[decision-rate-limiting-pg]] — PG fixed-window inside auth-service
- [[decision-admin-service-separation]] — separate admin-service for RBAC
- [[decision-audit-log-placement]] — audit logs in auth-service DB
- [[decision-oauth-csrf-stateless]] — stateless HMAC-based CSRF for OAuth

## Phase Dependencies

Phases 1+2 parallel → 3+4 parallel → 5+6+7 parallel

## Sources

- Implementation plan document

## Related

- [[auth-service]], [[admin-service]]
- [[auth-credential-entity]], [[jwt-manager]]
- [[security-and-authentication]]
