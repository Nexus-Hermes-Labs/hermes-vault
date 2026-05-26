---
title: Lint Report
tags: [lint, meta, quality]
date: 2026-05-26
status: active
---

# Lint Report — 2026-05-26

Automated quality check of the Hermes vault.

## Summary

| Category | Count |
|---|---|
| Broken links | 4 |
| Orphan pages (wiki layer) | 0 |
| Orphan pages (01-06 structure) | 23 |
| Missing `## Sources` sections | 0 |
| Contradictions | 1 |
| Missing entity pages | 4 |

## Broken Links

Links that point to pages that do not exist:

| Link | Referenced From | Action Needed |
|---|---|---|
| `[[grafana]]` | `concepts/observability-stack.md` | Create `entities/grafana.md` |
| `[[loki]]` | `concepts/observability-stack.md` | Create `entities/loki.md` |
| `[[prometheus]]` | `concepts/observability-stack.md` | Create `entities/prometheus.md` |
| `[[tempo]]` | `concepts/observability-stack.md`, `concepts/distributed-tracing.md` | Create `entities/tempo.md` |

**Note**: `[[related-page-1]]`, `[[related-page-2]]`, `[[source-page]]`, `[[target]]`, `[[wiki-links]]` appear in `CLAUDE.md` as template examples — these are not real broken links.

## Orphan Pages (01-06 Structure)

These pages exist in the pre-existing documentation structure (01-06 folders) but are not wiki-linked from any page. They serve as manual documentation and are referenced by path in CLAUDE.md and README.md, so they are not true orphans — but they could benefit from cross-linking.

| Page | Location | Notes |
|---|---|---|
| `overview` | `01-architecture/` | Could link from synthesis pages |
| `service-map` | `01-architecture/` | Covered by entity service pages |
| `tech-stack` | `01-architecture/` | Covered by `2026-05-26-technologies-doc` |
| `communication-patterns` | `01-architecture/` | Covered by `event-driven-architecture` synthesis |
| `observability` | `01-architecture/` | Covered by `observability-stack` concept |
| `roadmap-status` | `01-architecture/` | Covered by `2026-05-26-roadmap-doc` |
| `database` | `04-infrastructure/` | Covered by `postgresql` entity |
| `docker-and-networking` | `04-infrastructure/` | Covered by `2026-05-26-infra-docker-compose` |
| `nats` | `04-infrastructure/` | Covered by `nats-jetstream` entity |
| `event-contracts` | `03-domain/` | Covered by `event-driven-architecture` synthesis |
| `outbox-pattern` | `03-domain/` | Covered by `transactional-outbox` concept |
| `patterns` | `03-domain/` | Covered by `2026-05-26-patterns-doc` |
| `stub-services` | `02-services/` | Planned services, not yet active |
| `workspace-map` | `00-system/` | System file, referenced in README |
| `ai-instructions` | `00-system/` | System file, referenced in README |
| `ai-vault-maintenance-protocol` | `00-system/` | System file |
| `ADR-001-traefik-over-custom-gateway` | `06-decisions/` | Duplicate of `decision-traefik-edge-proxy` |
| `ADR-002-consul-rolled-back` | `06-decisions/` | Duplicate of `decision-consul-rolled-back` |
| `ADR-003-transactional-outbox` | `06-decisions/` | Duplicate of `decision-transactional-outbox` |

## Duplicate Content

The `06-decisions/` ADR files overlap with `decisions/` wiki pages:

| ADR (06-decisions/) | Wiki Page (decisions/) |
|---|---|
| `ADR-001-traefik-over-custom-gateway` | `decision-traefik-edge-proxy` |
| `ADR-002-consul-rolled-back` | `decision-consul-rolled-back` |
| `ADR-003-transactional-outbox` | `decision-transactional-outbox` |

**Recommendation**: Keep the `decisions/` wiki pages as canonical (they have proper frontmatter and cross-links). Consider archiving `06-decisions/` ADRs or merging content.

## Contradictions

| Pages | Description |
|---|---|
| `entities/chat-service.md` | Port discrepancy: ARCHITECTURE.md says 8084, Traefik routes.yml says 8094. Already documented as `## CONTRADICTION` in the page. See [[issue-port-discrepancy]]. |

## Missing Entity Pages

Referenced concepts/tools that lack their own entity page:

| Entity | Context |
|---|---|
| Grafana | Used in observability stack |
| Loki | Log aggregation |
| Prometheus | Metrics collection |
| Tempo | Distributed tracing backend |

## Code Duplication

[[chat-message-entity]] and [[messaging-message-entity]] share ~90% identical code. No shared trait or base. Documented in `domain-model-overview` synthesis as an open thread.

## Cross-Reference Quality

- Total wiki-style cross-references: **~350**
- All content pages have `## Sources` sections
- All content pages have `## Related` sections
- Bidirectional linking is maintained for all wiki-layer pages
