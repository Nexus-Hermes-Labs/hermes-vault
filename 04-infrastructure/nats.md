# NATS (JetStream)

**Version**: 2.10 | **Port**: 4222 (client), 8222 (monitoring) | **Container**: hermes-nats

## Role

Async event bus for cross-service domain events. Uses JetStream for durable, at-least-once delivery with replay capability.

## Streams

| Stream | Subjects | Retention | Max Age | Dedup Window |
|---|---|---|---|---|
| AUTH_EVENTS | `auth.>` | Limits | 7 days | 2 hours |
| CHAT_EVENTS | `chat.>` | Limits | 7 days | 2 hours |
| MESSAGING_EVENTS | `messaging.>` | Limits | 7 days | 2 hours |

All streams use file-backed storage. Config shared via `OutboxStreamConfig` so publisher and consumer never disagree.

## Subject Convention

`<domain>.<entity>.<action>` — e.g., `auth.user.registered`, `chat.message.created`

The `event_type` field in `EventEnvelope` doubles as the NATS subject — no mapping layer.

## Publishing

All services publish via the Transactional Outbox pattern (not inline `nats.publish`). See `03-domain/outbox-pattern.md`.

## Consuming

Two patterns:
- **Durable** (`JetStreamConsumerRunner`): For state changes. Idempotent via `processed_events` table. Stable `durable_name` required.
- **Ephemeral** (`ephemeral_fanout_consumer`): For live fanout. `deliver_policy: New`, `inactive_threshold: 60s`. No durability — missed events acceptable.

## Monitoring

NATS monitoring UI at `http://localhost:8222`.
