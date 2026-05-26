---
title: Security and Authentication
tags: [synthesis, security, authentication, jwt, traefik, mfa, oauth, webauthn, rbac]
source: multiple
date: 2026-05-26
status: active
---

# Security and Authentication

Cross-cutting synthesis of Hermes' security architecture: how users authenticate, how tokens flow, how services verify identity, and the planned security enhancements.

## Authentication Flow

1. **Login**: Client sends credentials to [[auth-service]] via HTTP
2. **Credential check**: [[auth-credential-entity]] validates against Argon2-hashed password (see [[decision-argon2-vs-sha256]])
3. **MFA check** (planned): If [[totp-secret-entity]] enabled → issue short-lived MFA JWT (5 min) → user submits TOTP code → real tokens issued. See [[multi-factor-authentication]].
4. **Token issuance**: [[jwt-manager]] generates access + refresh token pair
5. **Subsequent requests**: Client includes JWT in `Authorization` header

## ForwardAuth Pattern

All API traffic passes through [[traefik]] reverse proxy. Traefik's ForwardAuth middleware sends every request to [[auth-service]]'s verify endpoint before forwarding to the target service.

- **Only auth-service parses JWTs** — other services never touch tokens
- Auth-service extracts user identity and injects `X-User-Id`, `X-User-Role`, `X-User-Email` headers
- Target services read identity from trusted headers via [[request-context-module]]
- See [[decision-forward-auth]] for rationale

## Password Security

- **Argon2id** with tuned parameters (see [[decision-argon2-vs-sha256]])
- No plaintext passwords stored; credential entity only holds hash
- **Password policy** (planned): config-based validation — min/max length, complexity rules. See [[password-policy]].
- **Password history** (planned): prevents reuse of last N passwords. See [[password-history-entity]].
- Password change events published via [[transactional-outbox]]

## Token Management

- [[jwt-manager]] in common crate handles token creation and validation
- Access tokens: 6h, refresh tokens: 30d
- MFA tokens (planned): 5 min, typ="mfa", single-purpose
- Session tracking via auth_session entity in [[auth-service]]

## Multi-Factor Authentication (Planned)

Three methods planned for [[auth-service]]:

- **TOTP**: Time-based one-time passwords via authenticator app ([[totp-secret-entity]])
- **Backup codes**: 10 single-use recovery codes ([[mfa-backup-code-entity]])
- **WebAuthn/Passkeys**: FIDO2 hardware/biometric authentication ([[webauthn-credential-entity]])

See [[multi-factor-authentication]] and [[decision-mfa-separate-aggregate]].

## OAuth2 (Planned)

Google OAuth2 with find-or-create flow ([[oauth-account-entity]]):
- Match by provider_user_id → then by email → then create new user
- OAuth users get random password, skip email verification
- Stateless CSRF via HMAC-SHA256 ([[decision-oauth-csrf-stateless]])

## Rate Limiting

Two tiers. See [[rate-limiting]].
- **Traefik**: Global 100 req/s, burst 50
- **Auth-service** (planned): Per-endpoint PG fixed-window ([[rate-limit-bucket-entity]], [[decision-rate-limiting-pg]])

## Audit Logging (Planned)

Structured security event log in auth-service DB ([[audit-log-entity]]). Sync writes in UoW transactions for critical events, best-effort for informational. Admin-service reads via gRPC. See [[decision-audit-log-placement]].

## RBAC (Planned)

System-level role-based access control in [[admin-service]]:
- System roles: user, moderator, admin (distinct from [[guild-service]] per-guild bitflag roles)
- [[admin-role-entity]], [[admin-permission-entity]]
- See [[decision-admin-service-separation]]

## Open Issues

- [[issue-realtime-incomplete]] — WebSocket auth flow not yet implemented
- Auth integration test gaps (see memory: `project_auth_test_gaps.md`)

## Sources

- [[2026-05-26-auth-application-service]]
- [[2026-05-26-auth-credential-entity]]
- [[2026-05-26-jwt-manager]]
- [[2026-05-26-request-context]]
- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[service-architecture-overview]]
- [[gateway-authentication]]
- [[auth-service]]
- [[admin-service]]
- [[traefik]]
