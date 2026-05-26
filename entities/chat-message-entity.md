---
title: Message Entity — chat-service (Aggregate Root)
tags: [entity, domain, chat, message, aggregate-root]
source: hermes-be/services/chat-service/src/domain/message/entity.rs
date: 2026-05-26
status: active
---

# Message Entity — chat-service (Aggregate Root)

Domain aggregate root for the [[chat-service]]. Handles messages within guild channels only.

## Structure

| Field | Type | Notes |
|---|---|---|
| id | `Uuid` | Primary key |
| channel_id | `Uuid` | Target channel (always a guild channel) |
| user_id | `Uuid` | Author |
| content | `MessageContent` | Validated value object |
| message_type | `MessageType` | Text (no system message factory) |
| reply_to_id | `Option<Uuid>` | Thread support |
| edited_at | `Option<DateTime<Utc>>` | Set on edit |
| is_deleted | `bool` | Soft delete flag |

## Business Rules

- Author-only edit and delete (identical to messaging-service)
- Deleted messages cannot be edited or re-deleted
- No `new_system()` factory (unlike [[messaging-message-entity]])

## Key Difference from messaging-service

Chat-service messages always target a `channel_id` directly. No `MessageTarget` enum, no conversation support. Chat-service also has a separate `Reaction` entity not present in messaging-service.

## Sources

- [[2026-05-26-chat-message-entity]]

## Related

- [[chat-service]]
- [[messaging-message-entity]]
- [[messaging-service]]
- [[channel-entity]]
- [[aggregate-root]]
