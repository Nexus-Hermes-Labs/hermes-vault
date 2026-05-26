---
title: "Decision: Audit Logs in Auth-Service Database"
tags: [decision, auth, audit, storage]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: active
---

# Decision: Audit Logs in Auth-Service Database

## Context

Structured audit logs for security events (login, password change, MFA changes, account suspension) need a storage location. Options: auth-service DB, admin-service DB, or a dedicated audit service.

## Decision

Audit logs are stored in **auth-service's database** (`audit_logs` table). Admin-service reads them via gRPC `GetAuditLogs` RPC.

## Rationale

- Auth events are the primary audit source — writing in the same transaction (sync mode) guarantees consistency
- No additional network hop for the most frequent write path (login events)
- Admin-service can aggregate audit data from multiple sources via gRPC if needed later
- Follows Hermes's pattern of keeping data close to its producing service

## Write Modes

- **Sync** (`record`): Inside UoW transaction, for security-critical events. Guaranteed atomicity with the operation being audited.
- **Best-effort** (`record_best_effort`): Spawned Tokio task. For informational events where audit failure shouldn't block the operation.

## Alternatives Considered

- **Admin-service DB**: Requires cross-service write for every auth event — adds latency and failure mode
- **Dedicated audit service**: Over-engineered for current scale; could be extracted later if audit grows beyond auth events
- **Append-only log (Kafka/NATS)**: Adds infrastructure complexity; PG is sufficient and queryable

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[audit-log-entity]]
- [[auth-service]]
- [[admin-service]]
- [[unit-of-work]]
