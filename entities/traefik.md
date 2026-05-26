---
title: Traefik
tags: [entity, infrastructure, proxy, routing]
source: raw/codebase/traefik-routes.yml
date: 2026-05-26
status: active
---

# Traefik

Edge reverse proxy (v3.3) — single entry point for all external HTTP traffic.

- **Port**: 80 (web), 8080 (dashboard)
- **Config**: `infra/traefik/traefik.yml` (static), `infra/traefik/dynamic/routes.yml` (dynamic, hot-reload)

## Key Characteristics

- ForwardAuth middleware → [[auth-service]] `/internal/verify`
- Rate limiting: 100 req/s average, burst 50
- Docker provider for frontend auto-discovery
- File provider for backend route config
- Does NOT handle: JWT logic, event fanout, gRPC routing, WebSocket management

## Sources

- [[2026-05-26-traefik-routes]]
- [[2026-05-26-architecture-doc]]

## Related

- [[gateway-authentication]], [[decision-traefik-edge-proxy]]
