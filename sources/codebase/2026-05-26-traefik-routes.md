---
title: "Source: Traefik Dynamic Routes Configuration"
tags: [source, traefik, routing, infrastructure]
source: raw/codebase/traefik-routes.yml
date: 2026-05-26
status: active
---

# Source: Traefik Dynamic Routes Configuration

## Goal

Defines all HTTP routing rules, middleware chains, and backend service addresses for the Traefik edge proxy.

## Key Facts Extracted

- **Middlewares**: `jwt-auth` (ForwardAuth → auth-service:8081/internal/verify, injects X-User-Id/Role/Email), `rate-limit` (100 req/s, burst 50)
- **Routers** (by priority):
  - `realtime-ws` (50): `/ws` → realtime-service:8092 (rate-limit only, JWT on handshake)
  - `chat-channel-messages` (40): `/api/v1/channels/{id}/messages` → chat-service:8094
  - `chat-messages` (40): `/api/v1/messages` → chat-service:8094
  - `channel-nested` (30): `/api/v1/guilds/{id}/channels` → channel-service:8083
  - `auth-public` (20): `/api/v1/auth` → auth-service:8081 (rate-limit only)
  - `user-service` (20): `/api/v1/users` → user-service:8082
  - `messaging-conversations` (20): `/api/v1/conversations` → messaging-service:8084
  - `channel-service` (20): `/api/v1/channels` → channel-service:8083
  - `guild-service` (20): `/api/v1/guilds` + `/api/v1/invites` → guild-service:8086

## CONTRADICTION

The routing config shows chat-service on port **8094**, but ARCHITECTURE.md and ROADMAP.md list it on port **8084**. The messaging-service is listed as port **8084** in routes.yml. This is a port assignment discrepancy between docs and actual Traefik config.

## Entities Referenced

- [[traefik]], [[auth-service]], [[user-service]], [[guild-service]], [[channel-service]], [[chat-service]], [[messaging-service]], [[realtime-service]]

## Sources

- `raw/codebase/traefik-routes.yml`

## Related

- [[gateway-authentication]]
- [[decision-traefik-edge-proxy]]
