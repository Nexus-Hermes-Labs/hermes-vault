---
title: Repository Pattern
tags: [concept, ddd, persistence]
source: raw/docs/PATTERNS.md
date: 2026-05-26
status: active
---

# Repository Pattern

Generic `Repository<T, ID>` trait in `common` defines standard CRUD. Domain-specific repositories extend it with query methods relevant to their aggregate.

Traits defined in `domain/`, implementations in `infrastructure/persistence/postgres/`. Application services depend only on the trait — SQL implementation injected via `Arc<dyn Trait>`.

## Sources

- [[2026-05-26-patterns-doc]]

## Related

- [[domain-driven-design]], [[hexagonal-architecture]], [[unit-of-work]]
