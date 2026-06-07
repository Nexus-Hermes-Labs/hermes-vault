# auth-service

**Status**: Complete (core); enhancement in progress | **HTTP**: 8081 | **gRPC**: 50051

## Purpose

Handles all authentication: registration, login/logout, token management, email verification, password reset/change flows, and the ForwardAuth endpoint that Traefik calls on every protected request. Planned enhancements add MFA, OAuth, WebAuthn, and audit logging.

## Endpoints (Current)

| Method | Path | Auth | Purpose |
|---|---|---|---|
| POST | `/v1/auth/register` | none | Register with email + password (Argon2 hash) |
| POST | `/v1/auth/login` | none | Returns JWT access (6h) + refresh (30d) tokens |
| POST | `/v1/auth/refresh` | none | Rotate refresh token |
| POST | `/v1/auth/logout` | JWT | Invalidate session |
| POST | `/v1/auth/verify-email` | none | Email verification via token |
| POST | `/v1/auth/forgot-password` | none | Request password reset; returns 200 even when account does not exist |
| POST | `/v1/auth/reset-password` | none | Reset password with reset token + new password |
| POST | `/v1/auth/change-password` | Bearer token | Change current password while authenticated |
| GET | `/v1/auth/oauth/google` | none | Start Google login — returns `{ authorize_url }` (Architecture A) |
| POST | `/v1/auth/oauth/google/callback` | none | Exchange brokered `{ code, state }` → JWT tokens |
| GET | `/internal/verify` | Bearer token | ForwardAuth — called by Traefik, returns 200 + identity headers or 401 |

## Endpoints (Planned)

| Method | Path | Auth | Purpose |
|---|---|---|---|
| POST | `/v1/auth/mfa/setup` | JWT | Generate TOTP secret + otpauth URI |
| POST | `/v1/auth/mfa/confirm` | JWT | Activate TOTP with verification code |
| POST | `/v1/auth/mfa/disable` | JWT | Disable TOTP (requires current code) |
| POST | `/v1/auth/mfa/backup-codes` | JWT | Generate 10 backup codes |
| POST | `/v1/auth/mfa/challenge` | MFA token | Verify TOTP code, issue real tokens |
| POST | `/v1/auth/webauthn/register/begin` | JWT | Start WebAuthn registration |
| POST | `/v1/auth/webauthn/register/complete` | JWT | Complete WebAuthn registration |
| POST | `/v1/auth/webauthn/authenticate/begin` | none | Start WebAuthn authentication |
| POST | `/v1/auth/webauthn/authenticate/complete` | none | Complete WebAuthn authentication |
| GET | `/v1/auth/webauthn/credentials` | JWT | List WebAuthn credentials |
| DELETE | `/v1/auth/webauthn/credentials/:id` | JWT | Delete WebAuthn credential |
| POST | `/v1/auth/oauth/google/link` | JWT | Link Google account (explicit) |
| DELETE | `/v1/auth/oauth/google/unlink` | JWT | Unlink Google account |

## Domain Model

### Current
- **AuthCredential**: email, password_hash, status (active/suspended/unverified), role (user/admin), password reset fields, lockout fields
- **AuthSession**: user_id, refresh_token_hash (SHA-256), device_info, expires_at
- **PasswordHistoryEntry**: credential_id, password_hash, created_at; prevents recent password reuse
- **RateLimitBucket**: fixed-window counters for rate-limited auth flows
- **OAuthAccount**: credential_id, provider (`OAuthProvider` enum — Google only), provider_user_id, email; links a credential to an external identity
- **Value objects**: Email, PasswordHash, AccountStatus, SystemRole, OAuthProvider

### Planned Aggregates
- **TotpSecret**: credential_id, secret_encrypted (AES-256-GCM), enabled
- **MfaBackupCode**: credential_id, code_hash, used_at
- **WebAuthnCredential**: credential_id_bytes, user_id, public_key_json, sign_count
- **AuditLog**: credential_id, event_type, ip_address, user_agent, metadata

## Key Behaviors

- Password hashing: Argon2id (memory-hard)
- Token hashing: SHA-256 (fast, high-entropy tokens)
- JWT: HS256 with separate secrets for access and refresh tokens
- Refresh token rotation: old token invalidated on refresh
- Session stored in Redis with TTL matching refresh token expiry
- Account lockout after failed login attempts
- Password policy is enforced on registration, reset, and change: min length, uppercase, lowercase, digit, max length, and configurable special-character requirement
- Recent password reuse is rejected via password history
- Forgot-password is enumeration-safe and returns OK for unknown emails
- Auth rate limiting uses PostgreSQL fixed-window counters and returns `RATE_LIMITED` with retry-after seconds
- **Google OAuth login** (Architecture A — frontend owns `redirect_uri`): backend returns the same JWT JSON as `/login`. CSRF `state` + PKCE S256 verifier are held in Redis (`oauth:state:{state}`, 600s, single-use). Provider-verified emails auto-link to an existing credential; new emails create a verified OAuth-only credential and emit the `user.created` outbox event. Provider access is the `GoogleOAuthClient` port; config under `APP_OAUTH__GOOGLE__*` (optional — 503 when unset). See [[ADR-004-oauth-architecture]].
- (Planned) MFA gate in login flow: password OK + MFA enabled → MFA JWT → challenge → real tokens

## Cross-Service Communication

- **gRPC out → user-service**: `CreateUserProfile` on registration
- **gRPC in ← admin-service** (planned): SuspendUser, ActivateUser, UnlockUser, ForcePasswordReset, DisableMfa, ListUsers, GetUser, DeleteUser, GetAuditLogs
- **NATS publishes**: `auth.user.registered`, `auth.user.logged_in`, `auth.user.logged_out` (via Transactional Outbox)

## Background Tasks

- `EmailVerificationCleanupTask`: Hourly cleanup of expired verification tokens
- `OutboxPublisherTask`: Drains outbox events to NATS JetStream

## Database Tables

### Current
- `credentials` — user_id, email, password_hash, status, role, verification fields
- `sessions` — session management with refresh token hashes
- `outbox_events` — transactional outbox for event publishing
- `password_history` — credential_id, password_hash, created_at
- `rate_limit_buckets` — key, window_start, count, expires_at
- `oauth_accounts` — credential_id (FK), provider, provider_user_id, email; `UNIQUE(provider, provider_user_id)`

### Planned
- `totp_secrets` — credential_id, secret_encrypted, enabled
- `mfa_backup_codes` — credential_id, code_hash, used_at
- `webauthn_credentials` — credential_id_bytes, user_id, public_key_json, sign_count
- `audit_logs` — credential_id, event_type, ip_address, user_agent, metadata
