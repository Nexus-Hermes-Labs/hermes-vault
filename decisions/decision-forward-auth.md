---
title: "Decision: Centralized JWT via ForwardAuth"
tags: [decision, security, authentication]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# Decision: Centralized JWT via ForwardAuth

**Status**: Accepted

Only [[auth-service]] parses JWTs. All other services trust Traefik-injected `X-User-Id`, `X-User-Role`, `X-User-Email` headers. This works because services are only reachable through Traefik on the internal Docker network.

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-traefik-routes]]

## Related

- [[gateway-authentication]], [[jwt-manager]], [[traefik]]
