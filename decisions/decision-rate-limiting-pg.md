---
title: "Decision: PG Fixed-Window Rate Limiting"
tags: [decision, auth, rate-limiting, security]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: active
---

# Decision: PG Fixed-Window Rate Limiting

## Context

Security-sensitive auth endpoints (login, forgot-password, MFA challenge) need per-endpoint, per-key rate limiting beyond Traefik's global 100 req/s limit.

## Decision

Rate limiting uses **PostgreSQL fixed-window counters** inside auth-service. Single atomic `INSERT ... ON CONFLICT DO UPDATE RETURNING count` query per check.

## Rationale

- Auth-service already depends on PostgreSQL — no new infrastructure dependency
- Fixed-window is simple and sufficient for security rate limiting (not a high-precision billing system)
- Atomic single-query implementation avoids race conditions
- Redis would be faster but auth-service doesn't use Redis directly for writes (sessions use it, but through common crate infrastructure)
- Rate limiting is auth-service specific, not a cross-cutting concern for the common crate

## Traefik Relationship

| Layer | Scope | Limit |
|---|---|---|
| Traefik | Global, all endpoints | 100 req/s, burst 50 |
| Auth-service PG | Per-endpoint, per-key | e.g. 10 login attempts/min per IP |

Both layers operate independently. Traefik prevents volumetric attacks; auth-service rate limiting prevents credential stuffing.

## Alternatives Considered

- **Redis rate limiting**: Faster, but adds a write dependency on Redis to auth-service
- **Common crate middleware**: Rate limiting is auth-specific, not needed in guild/channel/user services
- **In-memory**: Doesn't survive restarts, doesn't work with multiple instances

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[rate-limit-bucket-entity]]
- [[traefik]]
- [[auth-service]]
