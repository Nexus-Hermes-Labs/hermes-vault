---
title: Messaging Message Entity — Domain Aggregate Root
tags: [source, domain, messaging, message, aggregate-root]
source: hermes-be/services/messaging-service/src/domain/message/entity.rs
date: 2026-05-26
status: active
---

# Messaging Message Entity — Domain Aggregate Root

## Goal

Document the Message aggregate in the messaging-service, which handles direct messages and conversations.

## What Was Done

Ingested `messaging-service/src/domain/message/entity.rs` (235 lines).

## Key Facts

- **Aggregate root** for the messaging-service bounded context
- Uses `MessageTarget` enum: `Channel(Uuid)` or `Conversation(Uuid)` — polymorphic targeting
- Fields: id, target, user_id, content (`MessageContent` value object), message_type (`MessageType` enum), reply_to_id, edited_at, is_deleted, timestamps
- **Construction**: `new()` for user messages (defaults to `MessageType::Text`); `new_system()` for system messages (uses `Uuid::nil()` as user_id)
- **Authorization**: `edit()` and `soft_delete()` enforce author-only via `is_author(requester_id)` check
- **Deleted messages** cannot be edited or re-deleted (returns `MessageError`)
- Convenience getters: `channel_id()` and `conversation_id()` extract from the `MessageTarget` enum
- Unit tests cover: creation, author-only edit, author-only delete

## Decisions

- Polymorphic `MessageTarget` enum instead of separate tables/entities for channel vs conversation messages
- System messages use `Uuid::nil()` as author — distinguishes from user-authored content

## Issues

None found.

## Open Threads

- No reaction support in messaging-service (unlike chat-service which has a separate `Reaction` entity)

## Sources

- [[2026-05-26-messaging-message-entity]] (this page) — raw symlink `raw/codebase/messaging-message-entity.rs`

## Related

- [[messaging-service]]
- [[channel-service]]
- [[chat-service]]
- [[aggregate-root]]
- [[domain-driven-design]]
