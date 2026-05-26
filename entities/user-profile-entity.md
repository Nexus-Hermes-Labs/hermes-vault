---
title: UserProfile Entity (Aggregate Root)
tags: [entity, domain, user, profile, aggregate-root, presence]
source: hermes-be/services/user-service/src/domain/user_profile/entity.rs
date: 2026-05-26
status: active
---

# UserProfile Entity (Aggregate Root)

Domain aggregate root for the [[user-service]]. Manages user identity, profile customization, and presence.

## Structure

| Field | Type | Notes |
|---|---|---|
| id | `Uuid` | Primary key |
| username | `Username` | Unique, value object |
| display_name | `String` | Non-unique, max 100 chars, supports emoji |
| avatar_url / banner_url | `Option<String>` | Must start with http/https |
| bio | `Option<String>` | Max 500 chars |
| status | `UserStatus` | Online/offline/etc. |
| custom_status_text | `Option<String>` | Max 128 chars |
| custom_status_emoji | `Option<String>` | Max 50 chars |
| custom_status_expires_at | `Option<DateTime<Utc>>` | Must be in future |
| last_seen_at | `Option<DateTime<Utc>>` | Updated on status change |
| last_username_changed_at | `Option<DateTime<Utc>>` | Rate limit tracking |

## Business Rules

- Username change rate-limited to once per 30 days
- Repository must verify username uniqueness before calling `change_username()`
- Display format: "Display Name (@username)"
- Custom status expires automatically; `has_custom_status_expired()` checks
- Soft delete pattern consistent with all aggregates

## Sources

- [[2026-05-26-user-profile-entity]]

## Related

- [[user-service]]
- [[auth-service]]
- [[auth-credential-entity]]
- [[aggregate-root]]
- [[domain-driven-design]]
