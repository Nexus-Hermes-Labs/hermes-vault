# Traefik Edge Proxy

**Version**: v3.3 | **Port**: 80 (web), 8080 (dashboard)

## Role

Single entry point for all external HTTP traffic. Handles routing, JWT validation, rate limiting, and CORS. Replaces a custom API gateway.

## Configuration

- **Static config**: `infra/traefik/traefik.yml` — entrypoints, dashboard, Docker + file providers
- **Dynamic routes**: `infra/traefik/dynamic/routes.yml` — routers, middlewares, backend services (hot-reload)
- **Frontend**: Discovered automatically via Docker labels on `hermes-network`
- **Backend**: Defined in the file provider (not via Docker labels)

## Middlewares

| Name | Type | Config |
|---|---|---|
| `jwt-auth` | ForwardAuth | `GET http://hermes-auth-service:8081/internal/verify` → injects `X-User-Id`, `X-User-Role`, `X-User-Email` |
| `rate-limit` | RateLimit | 100 req/s average, burst 50 |

## What Traefik Does NOT Do

- JWT logic (delegated to auth-service)
- Event fanout (realtime-service's job)
- gRPC routing (services talk directly on internal network)
- WebSocket management (realtime-service handles WS directly)
