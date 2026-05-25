# realtime-service

**Status**: ~50% | **HTTP**: 8092

## Purpose

WebSocket gateway that bridges NATS events to connected clients. Manages connections, subscriptions, and event fanout.

## Design

- `GET /ws?token=<access_token>` — WebSocket upgrade, JWT validated on handshake
- **Not through Traefik** for the WS connection (direct client → realtime-service)
- Connection state tracked in Redis for horizontal scaling

### Registries

- `ClientRegistry`: `DashMap<UserId, Vec<WsSender>>` — one user, multiple tabs/devices
- `SubscriptionRegistry`: `DashMap<ChannelId, HashSet<UserId>>` — which users are subscribed to which channels

### Client Opcodes

| Opcode | Direction | Purpose |
|---|---|---|
| SUBSCRIBE | client → server | Subscribe to channel events |
| UNSUBSCRIBE | client → server | Unsubscribe from channel |
| TYPING_START | client → server | Typing indicator |
| HEARTBEAT_ACK | client → server | Keepalive response |

### NATS Consumption

Uses `ephemeral_fanout_consumer` (no durability — missed events acceptable for live clients):
- `chat.message.created`
- `chat.message.updated`
- `chat.message.deleted`
- `messaging.reaction.added`
- `messaging.reaction.removed`

## Remaining Work

- Typing indicator fanout to other subscribers
- Heartbeat / keepalive mechanism
- Complete NATS → WebSocket bridge for all event types
- Reconnection handling
