# auth-service

**Status**: Complete | **HTTP**: 8081 | **gRPC**: 50051

## Purpose

Handles all authentication: registration, login/logout, token management, email verification, and the ForwardAuth endpoint that Traefik calls on every protected request.

## Endpoints

| Method | Path | Auth | Purpose |
|---|---|---|---|
| POST | `/v1/auth/register` | none | Register with email + password (Argon2 hash) |
| POST | `/v1/auth/login` | none | Returns JWT access (6h) + refresh (30d) tokens |
| POST | `/v1/auth/refresh` | none | Rotate refresh token |
| POST | `/v1/auth/logout` | JWT | Invalidate session |
| POST | `/v1/auth/verify-email` | none | Email verification via token |
| GET | `/internal/verify` | Bearer token | ForwardAuth — called by Traefik, returns 200 + identity headers or 401 |

## Domain Model

- **AuthCredential**: email, password_hash, status (active/suspended/unverified), role (user/admin)
- **AuthSession**: user_id, refresh_token_hash (SHA-256), device_info, expires_at
- **Value objects**: Email (validated format), PasswordHash, AccountStatus, SystemRole

## Key Behaviors

- Password hashing: Argon2id (memory-hard)
- Token hashing: SHA-256 (fast, high-entropy tokens)
- JWT: HS256 with separate secrets for access and refresh tokens
- Refresh token rotation: old token invalidated on refresh
- Session stored in Redis with TTL matching refresh token expiry
- Account lockout after failed login attempts

## Cross-Service Communication

- **gRPC out → user-service**: `CreateUserProfile` on registration
- **NATS publishes**: `auth.user.registered`, `auth.user.logged_in`, `auth.user.logged_out` (via Transactional Outbox)

## Background Tasks

- `EmailVerificationCleanupTask`: Hourly cleanup of expired verification tokens
- `OutboxPublisherTask`: Drains outbox events to NATS JetStream

## Database Tables

- `credentials` — user_id, email, password_hash, status, role, verification fields
- `sessions` — session management with refresh token hashes
- `outbox_events` — transactional outbox for event publishing
