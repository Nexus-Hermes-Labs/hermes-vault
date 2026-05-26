---
title: Gateway Authentication (ForwardAuth)
tags: [concept, security, authentication, traefik]
source: raw/docs/PATTERNS.md
date: 2026-05-26
status: active
---

# Gateway Authentication (ForwardAuth)

JWT validation is centralized at the [[traefik]] edge proxy via ForwardAuth middleware. No downstream service parses JWTs — they receive pre-validated identity as injected headers.

## Flow

1. Client sends `Authorization: Bearer <jwt>`
2. Traefik calls `GET auth-service:8081/internal/verify`
3. auth-service validates JWT via [[jwt-manager]]
4. On success: 200 + injects `X-User-Id`, `X-User-Role`, `X-User-Email`
5. On failure: 401 → Traefik rejects immediately
6. Downstream service reads headers via `RequestUser` Axum extractor

## Why This Works

- Services trust headers because requests can only reach them through Traefik on the internal Docker network
- No JWT library needed in downstream services
- Token rotation/revocation only needs to change auth-service

## Sources

- [[2026-05-26-patterns-doc]]
- [[2026-05-26-traefik-routes]]

## Related

- [[decision-forward-auth]], [[auth-service]], [[traefik]]
