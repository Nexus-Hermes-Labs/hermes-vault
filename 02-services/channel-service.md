# channel-service

**Status**: Complete | **HTTP**: 8083 | **gRPC**: 50053

## Purpose

Manages text and voice channels within guilds, channel categories, and channel-level permission overrides.

## Key Features

- Channel CRUD (text and voice types)
- Category management (grouping channels)
- Channel-level permission overrides (per-role or per-user)
- Guild ownership verification via gRPC to guild-service

## Cross-Service Communication

- **gRPC in** (from guild-service): `CreateDefaultChannels` — called on guild creation
- **gRPC out → guild-service**: `GetGuild` — verify guild exists and check ownership/permissions

## Database

- `channels` — name, type (text/voice), guild_id, category_id, position, topic
- `channel_categories` — name, guild_id, position
- `channel_permission_overrides` — channel_id, target_id, target_type (role/user), allow/deny bitflags
