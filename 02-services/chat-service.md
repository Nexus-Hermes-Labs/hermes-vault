# chat-service

**Status**: ~70% | **HTTP**: 8084

## Purpose

Handles message storage, history, reactions, attachments, and direct messages. Publishes message events to NATS for realtime-service consumption.

## Current State

- Domain layer: Complete
- Application layer: Complete
- Infrastructure: In progress (outbox publishing, persistence)
- Presentation: In progress (remaining HTTP endpoints and DTOs)
- Tests: Partial

## Key Features (planned)

- Message CRUD (create, edit, delete, pin)
- Reactions (add/remove emoji reactions)
- Attachments (metadata — actual files via media-service)
- Message history with pagination
- Direct messages between users
- Reply threading

## Event Publishing

Publishes to `CHAT_EVENTS` stream via Transactional Outbox:
- `chat.message.created`
- `chat.message.updated`
- `chat.message.deleted`

## Remaining Work

- Complete HTTP endpoint handlers and DTOs
- Full NATS event publishing for all message operations
- Comprehensive test coverage
