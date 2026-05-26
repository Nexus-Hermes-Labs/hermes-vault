---
title: auth-service
tags: [entity, service, authentication, mfa, oauth, webauthn]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# auth-service

Backend service handling all authentication: registration, login/logout, token management, email verification, MFA, OAuth, WebAuthn, and the ForwardAuth endpoint.

- **HTTP port**: 8081
- **gRPC port**: 50051
- **Status**: Complete (core); enhancement in progress
- **Codebase**: `hermes-be/services/auth-service/`

## Key Characteristics

- Aggregate root: [[auth-credential-entity]]
- Passwords hashed with Argon2id, tokens hashed with SHA-256
- JWT HS256 with separate access/refresh secrets
- Publishes events via [[transactional-outbox]] to `AUTH_EVENTS` stream
- gRPC client → [[user-service]] for profile creation on registration
- gRPC server → [[admin-service]] for credential management RPCs (suspend, activate, unlock, etc.)
- Background tasks: email verification cleanup (hourly), outbox publisher, rate limit bucket cleanup

## Planned Enhancements

- **Password Reset**: forgot/reset password endpoints (domain logic already exists in [[auth-credential-entity]])
- **Password Policy**: config-based validation rules ([[password-policy]])
- **Password History**: prevent reuse of last N passwords ([[password-history-entity]])
- **Rate Limiting**: per-endpoint PG fixed-window ([[rate-limit-bucket-entity]])
- **MFA/TOTP**: TOTP setup/verify/disable + backup codes ([[totp-secret-entity]], [[mfa-backup-code-entity]])
- **WebAuthn/Passkeys**: FIDO2 registration/authentication ([[webauthn-credential-entity]])
- **Google OAuth2**: find-or-create flow with account linking ([[oauth-account-entity]])
- **Audit Logging**: structured security event log ([[audit-log-entity]])

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-auth-application-service]]
- [[2026-05-26-auth-credential-entity]]
- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[jwt-manager]], [[traefik]], [[gateway-authentication]]
- [[admin-service]]
- [[multi-factor-authentication]], [[password-policy]], [[rate-limiting]]
