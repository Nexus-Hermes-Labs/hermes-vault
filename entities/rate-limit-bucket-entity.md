---
title: RateLimitBucket Entity
tags: [entity, domain, auth, rate-limiting, security]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# RateLimitBucket Entity

Infrastructure entity in [[auth-service]] for per-endpoint, per-key rate limiting using PostgreSQL fixed-window counters.

## Fields

- key (TEXT, composite PK part — e.g. "login:192.168.1.1")
- window_start (BIGINT, composite PK part — Unix epoch of window start)
- count (INTEGER, default 1)
- expires_at (TIMESTAMPTZ, for cleanup)

## Behavior

Single atomic query: `INSERT ... ON CONFLICT (key, window_start) DO UPDATE SET count = count + 1 RETURNING count`. If returned count exceeds limit, reject with HTTP 429 + `Retry-After` header.

## Rate Limit Keys

| Key Format | Limit | Purpose |
|---|---|---|
| `login:{ip}` | 10/min | Brute-force login prevention |
| `forgot_password:{email}` | 3/hour | Reset spam prevention |
| `mfa_challenge:{credential_id}` | 5/5min | MFA brute-force prevention |

## Relationship to Traefik Rate Limiting

Traefik applies global rate limiting (100 req/s, burst 50) at the edge. This per-endpoint rate limiting is finer-grained and operates inside auth-service for security-sensitive operations only.

## Cleanup

Background task (following `EmailVerificationCleanupTask` pattern): periodic `DELETE FROM rate_limit_buckets WHERE expires_at < NOW()`.

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[auth-service]]
- [[traefik]]
- [[decision-rate-limiting-pg]]
