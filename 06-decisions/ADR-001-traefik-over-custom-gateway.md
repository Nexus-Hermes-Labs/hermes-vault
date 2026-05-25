# ADR-001: Traefik Over Custom API Gateway

**Status**: Accepted
**Date**: Early 2026

## Context

With ~13 microservices, we needed centralized routing, authentication, and rate limiting. Options were:
1. Custom Rust API gateway service
2. Nginx with manual config management
3. Traefik with Docker auto-discovery

## Decision

Use Traefik v3 as the edge proxy.

## Rationale

- **ForwardAuth middleware** centralizes JWT validation in auth-service — no JWT logic duplicated across services
- **Docker provider** auto-discovers frontend container via labels
- **File provider** allows hot-reload of backend route changes
- **Battle-tested** reverse proxy vs. building and maintaining our own
- **Rate limiting** built-in (100 req/s, burst 50)
- Simpler than managing nginx configs for 13 services

## Consequences

- JWT parsing only happens in auth-service; all other services trust injected headers
- gRPC traffic goes directly between services (not through Traefik)
- WebSocket connections go directly to realtime-service (JWT validated on handshake)
- Adding a new HTTP service = adding an entry in `routes.yml`
