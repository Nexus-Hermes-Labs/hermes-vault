# auth-service

**Status**: Complete (core); enhancement in progress | **HTTP**: 8081 | **gRPC**: 50051

## Purpose

Handles all authentication: registration, login/logout, token management, email verification, and the ForwardAuth endpoint that Traefik calls on every protected request. Planned enhancements add MFA, OAuth, WebAuthn, password policy, rate limiting, and audit logging.

## Endpoints (Current)

| Method | Path | Auth | Purpose |
|---|---|---|---|
| POST | `/v1/auth/register` | none | Register with email + password (Argon2 hash) |
| POST | `/v1/auth/login` | none | Returns JWT access (6h) + refresh (30d) tokens |
| POST | `/v1/auth/refresh` | none | Rotate refresh token |
| POST | `/v1/auth/logout` | JWT | Invalidate session |
| POST | `/v1/auth/verify-email` | none | Email verification via token |
| GET | `/internal/verify` | Bearer token | ForwardAuth — called by Traefik, returns 200 + identity headers or 401 |

## Endpoints (Planned)

| Method | Path | Auth | Purpose |
|---|---|---|---|
| POST | `/v1/auth/forgot-password` | none | Request password reset (always 200) |
| POST | `/v1/auth/reset-password` | none | Reset with token + new password |
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
| GET | `/v1/auth/oauth/google` | none | Redirect to Google consent |
| GET | `/v1/auth/oauth/google/callback` | none | Google OAuth callback |
| POST | `/v1/auth/oauth/google/link` | JWT | Link Google account |
| DELETE | `/v1/auth/oauth/google/unlink` | JWT | Unlink Google account |

## Domain Model

### Current
- **AuthCredential**: email, password_hash, status (active/suspended/unverified), role (user/admin), password reset fields, lockout fields
- **AuthSession**: user_id, refresh_token_hash (SHA-256), device_info, expires_at
- **Value objects**: Email, PasswordHash, AccountStatus, SystemRole

### Planned Aggregates
- **PasswordHistoryEntry**: credential_id, password_hash, created_at
- **TotpSecret**: credential_id, secret_encrypted (AES-256-GCM), enabled
- **MfaBackupCode**: credential_id, code_hash, used_at
- **WebAuthnCredential**: credential_id_bytes, user_id, public_key_json, sign_count
- **OAuthAccount**: credential_id, provider, provider_user_id, email
- **AuditLog**: credential_id, event_type, ip_address, user_agent, metadata
- **RateLimitBucket**: key, window_start, count, expires_at

## Key Behaviors

- Password hashing: Argon2id (memory-hard)
- Token hashing: SHA-256 (fast, high-entropy tokens)
- JWT: HS256 with separate secrets for access and refresh tokens
- Refresh token rotation: old token invalidated on refresh
- Session stored in Redis with TTL matching refresh token expiry
- Account lockout after failed login attempts
- (Planned) MFA gate in login flow: password OK + MFA enabled → MFA JWT → challenge → real tokens
- (Planned) Per-endpoint rate limiting: PG fixed-window counters
- (Planned) Password policy: config-based validation rules

## Cross-Service Communication

- **gRPC out → user-service**: `CreateUserProfile` on registration
- **gRPC in ← admin-service** (planned): SuspendUser, ActivateUser, UnlockUser, ForcePasswordReset, DisableMfa, ListUsers, GetUser, DeleteUser, GetAuditLogs
- **NATS publishes**: `auth.user.registered`, `auth.user.logged_in`, `auth.user.logged_out` (via Transactional Outbox)

## Background Tasks

- `EmailVerificationCleanupTask`: Hourly cleanup of expired verification tokens
- `OutboxPublisherTask`: Drains outbox events to NATS JetStream
- (Planned) `RateLimitCleanupTask`: Periodic cleanup of expired rate limit buckets

## Database Tables

### Current
- `credentials` — user_id, email, password_hash, status, role, verification fields
- `sessions` — session management with refresh token hashes
- `outbox_events` — transactional outbox for event publishing

### Planned
- `password_history` — credential_id, password_hash, created_at
- `totp_secrets` — credential_id, secret_encrypted, enabled
- `mfa_backup_codes` — credential_id, code_hash, used_at
- `webauthn_credentials` — credential_id_bytes, user_id, public_key_json, sign_count
- `oauth_accounts` — credential_id, provider, provider_user_id, email
- `audit_logs` — credential_id, event_type, ip_address, user_agent, metadata
- `rate_limit_buckets` — key, window_start, count, expires_at
