# messaging-service

**Status**: In progress | **HTTP**: 8093

## Purpose

Message delivery orchestration. Handles reaction events and messaging-related domain logic that is separate from chat-service's message storage concerns.

## Event Publishing

Publishes to `MESSAGING_EVENTS` stream via Transactional Outbox:
- `messaging.reaction.added`
- `messaging.reaction.removed`

## Notes

- Present in Cargo workspace but not yet documented in `hermes-be/docs/ARCHITECTURE.md` or `ROADMAP.md`
- Has full DDD structure (domain, application, infrastructure, presentation, state, bootstrap)

> **Open Question:** The exact boundary between chat-service and messaging-service responsibilities needs clarification. Chat-service handles message CRUD; messaging-service appears to handle delivery/reaction orchestration.
