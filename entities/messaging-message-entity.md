---
title: Message Entity — messaging-service (Aggregate Root)
tags: [entity, domain, messaging, message, aggregate-root]
source: hermes-be/services/messaging-service/src/domain/message/entity.rs
date: 2026-05-26
status: active
---

# Message Entity — messaging-service (Aggregate Root)

Domain aggregate root for the [[messaging-service]]. Handles messages in both channels and conversations (DMs).

## Structure

| Field | Type | Notes |
|---|---|---|
| id | `Uuid` | Primary key |
| target | `MessageTarget` | `Channel(Uuid)` or `Conversation(Uuid)` |
| user_id | `Uuid` | Author (`Uuid::nil()` for system msgs) |
| content | `MessageContent` | Validated value object |
| message_type | `MessageType` | Text or System |
| reply_to_id | `Option<Uuid>` | Thread support |
| edited_at | `Option<DateTime<Utc>>` | Set on edit |
| is_deleted | `bool` | Soft delete flag |

## Business Rules

- Author-only edit and delete (`is_author()` check)
- Deleted messages cannot be edited or re-deleted
- System messages use `Uuid::nil()` as user_id
- Polymorphic targeting via `MessageTarget` enum

## Key Difference from chat-service

The messaging-service Message supports both channels and conversations via `MessageTarget`, while [[chat-message-entity]] only targets `channel_id`. ~90% code overlap between the two.

## Sources

- [[2026-05-26-messaging-message-entity]]

## Related

- [[messaging-service]]
- [[chat-message-entity]]
- [[chat-service]]
- [[aggregate-root]]
