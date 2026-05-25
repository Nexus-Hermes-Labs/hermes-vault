# Hermes Vault

Second brain for the Hermes ecosystem. This vault provides centralized, structured context for AI agents and human contributors working across Hermes projects.

## Purpose

Instead of reading entire codebases from scratch, AI tools (Claude Code, Codex, MCP agents) read this vault first to understand the project quickly. This reduces token usage, preserves decision history, and makes AI collaboration more consistent.

## How to Use

1. **Start here** — read this README
2. **System rules** — `00-system/` contains AI instructions, workspace map, and maintenance protocol
3. **Architecture** — `01-architecture/` covers system design, communication patterns, observability
4. **Services** — `02-services/` has per-service summaries with status, endpoints, and domain
5. **Domain** — `03-domain/` documents code patterns, event contracts, and data models
6. **Infrastructure** — `04-infrastructure/` covers Traefik, Docker, databases, caching, messaging
7. **Frontend** — `05-frontend/` describes the React SPA stack and structure
8. **Decisions** — `06-decisions/` holds ADRs (Architecture Decision Records)

## Ground Rules

- **Codebase is truth.** If vault and code disagree, trust the code and update the vault.
- **Vault is context.** It summarizes what matters for understanding the project, not every detail.
- **Keep it current.** When making architectural changes, update the vault in the same workflow.
- **Open questions stay open.** Don't write unconfirmed decisions as facts — mark them clearly.

## Quick Reference

| What | Where |
|---|---|
| Project overview | `01-architecture/overview.md` |
| Service map + ports | `01-architecture/service-map.md` |
| Code patterns (DDD) | `03-domain/patterns.md` |
| Tech stack rationale | `01-architecture/tech-stack.md` |
| Current roadmap status | `01-architecture/roadmap-status.md` |
| AI working instructions | `00-system/ai-instructions.md` |

## Vault Structure

```
hermes-vault/
├── README.md                          # This file
├── 00-system/
│   ├── ai-instructions.md            # How AI agents should use this vault
│   ├── workspace-map.md              # Repository layout and file locations
│   └── ai-vault-maintenance-protocol.md  # When and how to update vault
├── 01-architecture/
│   ├── overview.md                   # System architecture summary
│   ├── service-map.md               # All services, ports, status, relationships
│   ├── tech-stack.md                # Technology choices with rationale
│   ├── communication-patterns.md    # HTTP, gRPC, NATS, WebSocket patterns
│   ├── observability.md             # Logging, metrics, tracing stack
│   └── roadmap-status.md            # Phase status and completion tracking
├── 02-services/
│   ├── auth-service.md
│   ├── user-service.md
│   ├── guild-service.md
│   ├── channel-service.md
│   ├── chat-service.md
│   ├── messaging-service.md
│   ├── realtime-service.md
│   └── stub-services.md             # Presence, media, notification, search, ai, voice
├── 03-domain/
│   ├── patterns.md                   # DDD layers, repository, UoW, error handling
│   ├── event-contracts.md            # Domain events, subjects, envelope format
│   └── outbox-pattern.md            # Transactional outbox + idempotent consumer
├── 04-infrastructure/
│   ├── traefik.md                    # Edge proxy, routing, ForwardAuth, rate limiting
│   ├── docker-and-networking.md      # Compose layout, hermes-network, service discovery
│   ├── database.md                   # PostgreSQL setup, migrations, per-service schemas
│   ├── redis.md                      # Caching strategy, session storage, presence
│   └── nats.md                       # JetStream, streams, subjects, consumer patterns
├── 05-frontend/
│   └── overview.md                   # React + Vite + TypeScript stack
└── 06-decisions/
    ├── ADR-001-traefik-over-custom-gateway.md
    ├── ADR-002-consul-rolled-back.md
    └── ADR-003-transactional-outbox.md
```
