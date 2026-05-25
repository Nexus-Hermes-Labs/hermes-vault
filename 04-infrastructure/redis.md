# Redis

**Version**: 7 | **Port**: 6379 | **Container**: hermes-redis

## Current Uses

| Use | Service | TTL |
|---|---|---|
| Session storage | auth-service | Matches refresh token expiry (30d) |
| Refresh token hashes | auth-service | Same as session |

## Planned Uses (Not Yet Activated)

| Use | Service | TTL |
|---|---|---|
| User profile cache | user-service | 1 hour |
| Relationship cache | user-service | 5 minutes |
| Presence tracking | presence-service | Live (short heartbeat TTL) |
| Connection state | realtime-service | Live |
| Message cache | chat-service | Last 50 per channel |
| Rate-limit counters | Various | Sliding window |

## Connection

`redis = { features = ["tokio-comp", "connection-manager"] }` — `ConnectionManager` constructed once per service in `bootstrap/`.

## Note

Infrastructure is wired in all services, but caching logic (profile, relationship, message caches) is not yet implemented. Only auth-service actively uses Redis for session management.
