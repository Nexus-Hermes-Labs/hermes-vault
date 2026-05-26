---
title: "Decision: Stateless HMAC-Based OAuth CSRF"
tags: [decision, auth, oauth, security]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: active
---

# Decision: Stateless HMAC-Based OAuth CSRF

## Context

OAuth2 authorization code flow requires CSRF protection via a `state` parameter. The state must be verifiable on callback without being forgeable by an attacker.

## Decision

Use **stateless CSRF**: `Base64(nonce || HMAC-SHA256(nonce, jwt_secret))`. No database or Redis storage needed.

## Flow

1. On redirect to Google: generate random nonce, compute HMAC with JWT secret, encode as `state`
2. On callback: split state into nonce + HMAC, recompute HMAC with JWT secret, compare
3. If match → valid state (originated from this server). If not → reject.

## Rationale

- Zero storage overhead (no DB table, no Redis key)
- JWT secret is already available in auth-service config
- HMAC-SHA256 is unforgeable without the secret
- Nonce provides uniqueness; HMAC provides authenticity
- Simple implementation: ~10 lines of code

## Alternatives Considered

- **DB-stored state**: Requires migration, cleanup task, adds write per OAuth flow
- **Redis-stored state**: Works but adds a Redis dependency for a simple check
- **Session-stored state**: Auth-service doesn't use server-side sessions for OAuth flow

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[oauth-account-entity]]
- [[auth-service]]
- [[jwt-manager]]
