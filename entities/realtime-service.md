---
title: realtime-service
tags: [entity, service, websocket, realtime]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# realtime-service

WebSocket gateway bridging NATS events to connected clients.

- **HTTP port**: 8092
- **Status**: ~50%

## Key Characteristics

- `GET /ws?token=<access_token>` — JWT validated on handshake, not via Traefik ForwardAuth
- `ClientRegistry`: `DashMap<UserId, Vec<WsSender>>` (multi-device)
- `SubscriptionRegistry`: `DashMap<ChannelId, HashSet<UserId>>`
- Ephemeral NATS consumer (missed events acceptable for live clients)
- Remaining: typing indicator, heartbeat/keepalive

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-traefik-routes]]

## Related

- [[chat-service]], [[messaging-service]], [[nats-jetstream]]
