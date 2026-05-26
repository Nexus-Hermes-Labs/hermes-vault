# ADR-004: Auth Enhancement Architecture Decisions

**Status**: Proposed (May 2026)
**Scope**: auth-service, admin-service (new)

## Context

Hermes auth-service handles basic authentication. Enterprise features (MFA, OAuth, password policy, WebAuthn, RBAC, audit logging) are needed. This ADR covers the six architectural decisions made during planning.

## Decisions

### 4a. MFA as Separate Aggregates

TOTP secrets, backup codes, WebAuthn credentials, OAuth accounts, and password history are each separate domain aggregates — not extensions to AuthCredential (which already has 19 fields). See [[decision-mfa-separate-aggregate]].

### 4b. Config-Based Password Policy

Password validation rules loaded from `auth-service.toml` `[password_policy]` section. No database storage — Hermes is single-tenant. See [[decision-password-policy-config]].

### 4c. PG Fixed-Window Rate Limiting

Per-endpoint rate limiting uses PostgreSQL fixed-window counters inside auth-service. Single atomic `INSERT ... ON CONFLICT DO UPDATE RETURNING count` query. Complements Traefik's global 100 req/s. See [[decision-rate-limiting-pg]].

### 4d. Separate Admin Service for RBAC

New admin-service (port 8095/50058) owns system-level RBAC. Delegates credential mutations to auth-service via gRPC. System roles (user/moderator/admin) are distinct from guild-service's per-guild bitflag roles. See [[decision-admin-service-separation]].

### 4e. Audit Logs in Auth-Service Database

Structured audit logs stored in auth-service DB for transactional consistency. Admin-service reads via gRPC `GetAuditLogs`. Sync writes in UoW for critical events, best-effort for informational. See [[decision-audit-log-placement]].

### 4f. Stateless HMAC-Based OAuth CSRF

OAuth2 state parameter uses `Base64(nonce || HMAC-SHA256(nonce, jwt_secret))`. Zero storage overhead. See [[decision-oauth-csrf-stateless]].

## Consequences

- Auth-service gains 6 new aggregates, 7 new database tables, ~15 new HTTP endpoints
- Admin-service is a new microservice requiring full 6-layer DDD scaffold
- JWT manager gains MFA token type variant
- UoW trait extends with 6 new writer accessors
- 3 new Rust dependencies (totp-lite, webauthn-rs, oauth2) plus supporting crates
