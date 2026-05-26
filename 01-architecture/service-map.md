# Service Map

## Active Services

| Service | HTTP | gRPC | Status | Domain |
|---|---|---|---|---|
| auth-service | 8081 | 50051 | Complete | Register, login, logout, refresh, email verify, ForwardAuth |
| user-service | 8082 | 50052 | Complete | Profiles, friends, blocks, privacy, badges |
| guild-service | 8086 | 50056 | Complete | Guild CRUD, roles, members, invites, permissions |
| channel-service | 8083 | 50053 | Complete | Text/voice channels, categories, permission overrides |
| chat-service | 8084 | — | ~70% | Messages, reactions, attachments, history, DMs |
| messaging-service | 8093 | — | In progress | Message delivery orchestration |
| realtime-service | 8092 | — | ~50% | WebSocket gateway, NATS fanout to clients |

## Planned Services

| Service | Port | Phase | Domain |
|---|---|---|---|
| admin-service | 8095 / 50058 | Auth Enhancement | System-level RBAC, user management, audit |

## Stub Services (not started)

| Service | Port | Phase | Domain |
|---|---|---|---|
| presence-service | 8087 | Phase 2 | Online/offline/idle tracking, typing indicators |
| media-service | 8088 | Phase 2 | File uploads, image processing, avatars |
| notification-service | 8089 | Phase 2 | Push notifications, unreads, mentions |
| search-service | 8090 | Phase 4 | Full-text search (messages, users, guilds) |
| ai-service | 8091 | Phase 3 | Real-time translation (text, STT, TTS) |
| voice-service | 8085 | Phase 4 | WebRTC signaling, voice channels |

## Infrastructure Services

| Container | Port(s) | Purpose |
|---|---|---|
| hermes-postgres | 5432 | PostgreSQL 16 — per-service databases in shared instance |
| hermes-redis | 6379 | Redis 7 — sessions, caching, presence |
| hermes-nats | 4222 / 8222 | NATS 2.10 + JetStream — async event bus |
| hermes-prometheus | 9090 | Metrics scraping (15s interval) |
| hermes-loki | 3100 | Log aggregation (7-day retention) |
| hermes-promtail | — | Docker log collector → Loki |
| hermes-tempo | 3200 / 4317 | Distributed trace storage (OTLP/gRPC) |
| hermes-grafana | 3000 | Dashboards (admin/admin) — Prometheus + Loki + Tempo |
| hermes-mailpit | 1025 / 8025 | SMTP catcher for dev email |

## Service-to-Service Relationships

### Synchronous (gRPC)

```
auth-service → user-service (CreateUserProfile on registration)
channel-service → guild-service (GetGuild / ownership verification)
guild-service → channel-service (CreateDefaultChannels)
guild-service → user-service (GetUserProfile)
```

### Asynchronous (NATS JetStream)

```
auth-service publishes → AUTH_EVENTS stream:
  - auth.user.registered
  - auth.user.logged_in
  - auth.user.logged_out

chat-service publishes → CHAT_EVENTS stream:
  - chat.message.created
  - chat.message.updated
  - chat.message.deleted

messaging-service publishes → MESSAGING_EVENTS stream:
  - messaging.reaction.added
  - messaging.reaction.removed

realtime-service consumes (ephemeral) → all of the above for WebSocket fanout
user-service consumes (durable) → user.created for profile materialization
```

## Shared Crates

| Crate | Purpose |
|---|---|
| common | JWT manager, observability (tracing + metrics), NATS, Redis helpers, outbox, domain errors, middleware |
| common-config | Figment-based config loading from .env + env vars |
