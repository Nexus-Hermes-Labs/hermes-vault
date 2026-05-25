# Event Contracts

## Envelope Format

All domain events are wrapped in `EventEnvelope<T>`:

```rust
EventEnvelope<T: DomainEvent> {
    event_id: Uuid,          // unique per event
    event_type: String,      // doubles as NATS subject
    aggregate_id: Uuid,
    version: u32,
    occurred_at: DateTime<Utc>,
    source_service: String,
    correlation_id: Option<Uuid>,  // for distributed tracing
    payload: T,
}
```

## Streams and Subjects

| Stream | Subjects | Producer | Retention |
|---|---|---|---|
| AUTH_EVENTS | `auth.>` | auth-service | 7 days, file storage |
| CHAT_EVENTS | `chat.>` | chat-service | 7 days, file storage |
| MESSAGING_EVENTS | `messaging.>` | messaging-service | 7 days, file storage |

Stream config: `retention: Limits`, `storage: File`, 7-day `max_age`, 2-hour `duplicate_window`.

## Event Types

### auth-service → AUTH_EVENTS

| Subject | Payload | When |
|---|---|---|
| `auth.user.registered` | `{ user_id, email, role }` | After successful registration |
| `auth.user.logged_in` | `{ user_id }` | After successful login |
| `auth.user.logged_out` | `{ user_id }` | After logout |

### chat-service → CHAT_EVENTS

| Subject | Payload | When |
|---|---|---|
| `chat.message.created` | `{ message_id, channel_id, author_id, content }` | New message sent |
| `chat.message.updated` | `{ message_id, channel_id, content }` | Message edited |
| `chat.message.deleted` | `{ message_id, channel_id }` | Message deleted |

### messaging-service → MESSAGING_EVENTS

| Subject | Payload | When |
|---|---|---|
| `messaging.reaction.added` | `{ message_id, user_id, emoji }` | Reaction added |
| `messaging.reaction.removed` | `{ message_id, user_id, emoji }` | Reaction removed |

## Consumers

| Consumer | Stream | Type | Purpose |
|---|---|---|---|
| user-service | AUTH_EVENTS | Durable | Profile materialization from `user.created` |
| realtime-service | CHAT_EVENTS, MESSAGING_EVENTS | Ephemeral | WebSocket fanout to live clients |

## Deduplication

- **Publisher side**: JetStream `Nats-Msg-Id = event_id` rejects duplicates within the 2-hour `duplicate_window`
- **Consumer side**: `processed_events` table with `event_id` PK — insert fails on duplicate, event is skipped
