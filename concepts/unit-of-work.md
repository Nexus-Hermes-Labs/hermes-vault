---
title: Unit of Work Pattern
tags: [concept, ddd, transactions, persistence]
source: raw/docs/PATTERNS.md
date: 2026-05-26
status: active
---

# Unit of Work Pattern

Coordinates multi-step writes that must succeed or fail atomically. Solves the problem of sharing a `sqlx::Transaction` across multiple repository calls in async code.

## Implementation

- `UnitOfWork` base trait: `commit(self: Box<Self>)` / `rollback(self: Box<Self>)`
- Per-service UoW trait extends base, adds writer accessors (e.g., `credentials()`, `sessions()`, `outbox()`)
- `run_in_transaction(uow, closure)` helper: commits on Ok, rolls back on Err
- `SharedTx = Arc<Mutex<Option<Transaction>>>` lets multiple writers share one transaction concurrently
- Application services always call `transaction()` — manual begin/commit is a code smell

## Sources

- [[2026-05-26-patterns-doc]]

## Related

- [[domain-driven-design]], [[transactional-outbox]], [[repository-pattern]]
