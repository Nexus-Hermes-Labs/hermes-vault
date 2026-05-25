# Communication Patterns

## Client → Service (HTTP REST via Traefik)

All client-facing traffic enters through Traefik on port 80. Traefik handles:
- Path-based routing to backend services
- JWT validation via ForwardAuth (delegates to auth-service)
- Rate limiting (100 req/s, burst 50)
- Header injection: `X-User-Id`, `X-User-Role`, `X-User-Email`

Backend services read identity via the `RequestUser` Axum extractor — no JWT parsing outside auth-service.

### Routing Table

| Path | Service | Auth |
|---|---|---|
| `/` | frontend :3001 | none |
| `/api/v1/auth/*` | auth-service :8081 | rate-limit only |
| `/api/v1/users/*` | user-service :8082 | ForwardAuth + rate-limit |
| `/api/v1/guilds/*` | guild-service :8086 | ForwardAuth + rate-limit |
| `/api/v1/invites/*` | guild-service :8086 | ForwardAuth + rate-limit |
| `/api/v1/channels/*` | channel-service :8083 | ForwardAuth + rate-limit |
| `/api/v1/guilds/{id}/channels` | channel-service :8083 | ForwardAuth (priority 30) |

## Service → Service (gRPC)

Direct container-to-container calls, not through Traefik. Used when one service needs an immediate response during request handling.

- Proto definitions: per-service `proto/` directories
- Code generation: `build.rs` with `tonic-build`
- Request ID propagation: `RequestIdInterceptor` injects `x-request-id` + W3C `traceparent`
- Generated code: checked into `src/presentation/grpc/proto/`

### Active gRPC Calls

| Caller | Callee | Method | Purpose |
|---|---|---|---|
| auth-service | user-service :50052 | CreateUserProfile | Profile creation on registration |
| channel-service | guild-service :50056 | GetGuild | Ownership/permission verification |
| guild-service | channel-service :50053 | CreateDefaultChannels | Default channels on guild creation |
| guild-service | user-service :50052 | GetUserProfile | Profile lookup for member display |

## Async Events (NATS JetStream)

Cross-service events published via the Transactional Outbox pattern (see `03-domain/outbox-pattern.md`).

### Streams

| Stream | Subjects | Producers |
|---|---|---|
| AUTH_EVENTS | `auth.>` | auth-service |
| CHAT_EVENTS | `chat.>` | chat-service |
| MESSAGING_EVENTS | `messaging.>` | messaging-service |

### Consumer Types

- **Durable** (JetStreamConsumerRunner): For state changes that must not be lost (e.g., user profile materialization). Idempotent via `processed_events` table.
- **Ephemeral** (ephemeral_fanout_consumer): For real-time fanout where missed events are acceptable (e.g., WebSocket delivery).

## Real-Time (WebSocket)

`realtime-service` manages WebSocket connections directly (not through Traefik for the WS upgrade path):

- `GET /ws?token=<access_token>` — JWT validated on handshake
- `ClientRegistry`: `DashMap<UserId, Vec<WsSender>>` (multi-device)
- `SubscriptionRegistry`: `DashMap<ChannelId, HashSet<UserId>>`
- Client opcodes: `SUBSCRIBE`, `UNSUBSCRIBE`, `TYPING_START`, `HEARTBEAT_ACK`
- Server pushes NATS events to subscribed WebSocket clients
