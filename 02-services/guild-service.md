# guild-service

**Status**: Complete | **HTTP**: 8086 | **gRPC**: 50056

## Purpose

Manages guilds (servers), roles with bitflag-based permissions, member management, and invite links.

## Key Features

- Guild CRUD (create, update, delete, transfer ownership)
- Role management with bitflag permission system (i64 with `grant()`, `revoke()`, `has()`)
- Member management (join, leave, kick, ban)
- Invite system with expiry dates and usage limits
- Default channels created on guild creation (via gRPC to channel-service)

## Endpoints

- `POST/GET/PATCH/DELETE /v1/guilds/*` — Guild CRUD
- `POST/GET/PATCH/DELETE /v1/guilds/{id}/roles/*` — Role management
- `POST/GET/DELETE /v1/guilds/{id}/members/*` — Member management
- `POST/GET/DELETE /v1/invites/*` — Invite management

## Cross-Service Communication

- **gRPC out → channel-service**: `CreateDefaultChannels` on guild creation
- **gRPC out → user-service**: `GetUserProfile` for member display
- **gRPC in** (from channel-service): `GetGuild` for ownership/permission checks

## Database

- `guilds` — name, description, icon, owner_id
- `guild_roles` — name, permissions (i64 bitflags), position
- `guild_members` — user_id, guild_id, joined_at, role assignments
- `guild_invites` — code, expires_at, max_uses, use_count
