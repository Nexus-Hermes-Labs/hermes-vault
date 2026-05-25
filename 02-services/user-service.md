# user-service

**Status**: Complete | **HTTP**: 8082 | **gRPC**: 50052

## Purpose

Manages user profiles, relationships (friends/blocks), privacy settings, and discriminators. Provides gRPC server for cross-service user lookups.

## Key Features

- User profile CRUD (display name, avatar, bio, discriminator)
- Friend system (send/accept/reject/remove)
- Block system (prevents friend requests and interactions)
- Privacy settings per user
- Badge system
- gRPC server: `GetUserProfile` called by guild-service and others

## Cross-Service Communication

- **gRPC in** (from auth-service): `CreateUserProfile` — materializes profile on registration
- **gRPC in** (from guild-service): `GetUserProfile` — profile data for member display
- **NATS consumes** (durable): `user.created` events for profile materialization

## Database

- `user_profiles` — display_name, avatar_url, bio, discriminator, privacy settings
- `friendships` — bidirectional relationships (PostgreSQL triggers handle sync)
- `blocks` — user_id → blocked_user_id
- `processed_events` — idempotent event consumption
