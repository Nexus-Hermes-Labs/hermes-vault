---
title: Hexagonal Architecture
tags: [concept, architecture, ports-and-adapters]
source: raw/docs/PATTERNS.md
date: 2026-05-26
status: active
---

# Hexagonal Architecture

Also known as Ports and Adapters. Domain logic defines interfaces (ports); infrastructure provides implementations (adapters). This allows swapping databases, message brokers, or transport layers without touching domain code.

In Hermes, repository traits are defined in `domain/`, their Postgres implementations live in `infrastructure/persistence/postgres/`. Application services depend only on traits — SQL implementation is injected via `Arc<dyn Trait>`.

## Sources

- [[2026-05-26-patterns-doc]]

## Related

- [[domain-driven-design]], [[repository-pattern]]
