---
title: Rate Limiting
tags: [concept, security, rate-limiting]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# Rate Limiting

Two-tier rate limiting in Hermes: global at [[traefik]] edge proxy, per-endpoint inside [[auth-service]].

## Tier 1: Traefik (Global)

- 100 requests/second, burst 50
- Applies to all routes
- Prevents volumetric attacks

## Tier 2: Auth-Service (Per-Endpoint)

- PostgreSQL fixed-window counters
- Per-key granularity (IP, email, credential_id)
- Targets security-sensitive operations only

| Endpoint | Key | Limit |
|---|---|---|
| Login | IP address | 10/min |
| Forgot password | Email | 3/hour |
| MFA challenge | Credential ID | 5/5min |

## Implementation

Single atomic query: `INSERT ... ON CONFLICT DO UPDATE SET count = count + 1 RETURNING count`. If count exceeds limit, return HTTP 429 with `Retry-After` header.

## Sources

- [[2026-05-26-auth-enhancement-plan]]
- [[decision-rate-limiting-pg]]
- [[2026-05-26-traefik-routes]]

## Related

- [[rate-limit-bucket-entity]]
- [[auth-service]]
- [[traefik]]
