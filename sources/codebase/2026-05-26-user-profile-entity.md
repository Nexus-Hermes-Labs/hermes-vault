---
title: User Profile Entity — Domain Aggregate Root
tags: [source, domain, user, profile, aggregate-root, presence]
source: hermes-be/services/user-service/src/domain/user_profile/entity.rs
date: 2026-05-26
status: active
---

# User Profile Entity — Domain Aggregate Root

## Goal

Document the UserProfile aggregate root which manages user identity, profile, and presence.

## What Was Done

Ingested `user-service/src/domain/user_profile/entity.rs` (449 lines).

## Key Facts

- **Aggregate root** for the user-service bounded context
- **Identity**: id, unique username (`Username` value object), non-unique display_name
- **Profile**: avatar_url, banner_url, bio — all optional, validated on update
- **Presence**: status (`UserStatus`), custom_status_text/emoji/expires_at, last_seen_at
- **Username change**: rate-limited to once per 30 days (tracked via `last_username_changed_at`)
- **Construction**: `new()` validates display_name (1-100 chars); `from_persisted()` for DB rehydration
- **Business rules**:
  - `update_profile()` — partial update, validates display_name (max 100), bio (max 500), URLs (must start with http/https)
  - `change_username()` — 30-day rate limit; repository must verify uniqueness before calling
  - `set_custom_status()` — text max 128, emoji max 50, expiration must be in the future
  - `clear_custom_status()` — removes all custom status fields
  - `has_custom_status_expired()` — checks if expiration has passed
- **Soft delete** pattern, consistent with other aggregates
- Display helpers: `full_display()` returns "Display Name (@username)", `mention()` returns "@username"
- Unit tests cover: creation, display format, profile update, username rate limit, custom status, delete

## Decisions

- Simplified identity: username-based (no discriminator like Discord's old system)
- Display name is separate from username — non-unique, supports emoji
- URL validation is minimal (http/https prefix check only)

## Issues

None found — rich, well-tested aggregate.

## Open Threads

- `user_privacy` and `user_relationship` are separate aggregates in the same service (not yet ingested)

## Sources

- [[2026-05-26-user-profile-entity]] (this page) — raw symlink `raw/codebase/user-profile-entity.rs`

## Related

- [[user-service]]
- [[auth-service]]
- [[auth-credential-entity]]
- [[aggregate-root]]
- [[domain-driven-design]]
