---
title: Chat Message Entity — Domain Aggregate Root
tags: [source, domain, chat, message, aggregate-root]
source: hermes-be/services/chat-service/src/domain/message/entity.rs
date: 2026-05-26
status: active
---

# Chat Message Entity — Domain Aggregate Root

## Goal

Document the Message aggregate in the chat-service, which handles guild channel messages.

## What Was Done

Ingested `chat-service/src/domain/message/entity.rs` (192 lines).

## Key Facts

- **Aggregate root** for the chat-service bounded context
- Always belongs to a guild channel (`channel_id: Uuid`) — no conversation support (that's messaging-service)
- Fields: id, channel_id, user_id, content (`MessageContent`), message_type (`MessageType`), reply_to_id, edited_at, is_deleted, timestamps
- **Construction**: `new()` for user messages; no `new_system()` factory (unlike messaging-service)
- **Authorization**: `edit()` and `soft_delete()` enforce author-only check, identical to messaging-service
- Nearly identical structure to messaging-service's Message but scoped to channels only
- Unit tests cover: creation, author-only edit, author-only delete

## Decisions

- **Separate bounded context**: chat-service handles guild channel messages; messaging-service handles DMs/conversations
- Chat-service also has a `Reaction` entity (69 lines) — not present in messaging-service

## Issues

- **Duplication concern**: chat-service and messaging-service Message entities share ~90% identical code (same fields, same business logic). No shared trait or base implementation exists.

## Open Threads

- Reaction entity in chat-service should be ingested separately
- Consider whether chat and messaging message entities should share a common trait

## Sources

- [[2026-05-26-chat-message-entity]] (this page) — raw symlink `raw/codebase/chat-message-entity.rs`

## Related

- [[chat-service]]
- [[messaging-service]]
- [[channel-service]]
- [[2026-05-26-messaging-message-entity]]
- [[aggregate-root]]
- [[domain-driven-design]]
