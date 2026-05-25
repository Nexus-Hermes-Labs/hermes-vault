# Workspace Map

## Repository Layout

Hermes is a multi-repo workspace managed from a single root:

```
hermes/                              # Root orchestrator
├── Makefile                         # make dev / up / down / logs / clean / prune
├── docker-compose.yml               # Traefik + hermes-network creation
├── infra/traefik/                   # Traefik static + dynamic config
├── docs/
│   ├── ARCHITECTURE.md              # Full system architecture reference
│   └── TECHNOLOGIES.md             # Tech stack with rationale
├── hermes-be/                       # Backend (git submodule → github.com/bulutcan99/hermes-be)
│   ├── Cargo.toml                   # Rust workspace: 14 crates, lint policy, profiles
│   ├── Makefile                     # Backend-specific commands
│   ├── docker-compose.yml           # All Rust services + stub whoami containers
│   ├── .env / .env.example          # Environment config (plain .env, no Consul)
│   ├── config/                      # Figment TOML config files
│   ├── proto/                       # Shared Protocol Buffer definitions
│   ├── infra/                       # Infrastructure container configs
│   │   ├── postgres/init.sql        # Per-service database creation
│   │   ├── prometheus/              # Scrape config
│   │   ├── loki/                    # Log aggregation config
│   │   ├── promtail/                # Log collector config
│   │   ├── tempo/                   # Distributed tracing config
│   │   ├── grafana/                 # Dashboards + datasource provisioning
│   │   └── docker-compose.yml       # All infra containers
│   ├── services/
│   │   ├── common/                  # Shared library (JWT, observability, outbox, errors)
│   │   ├── common/common-config/    # Figment-based config loading
│   │   ├── auth-service/            # :8081 / gRPC :50051
│   │   ├── user-service/            # :8082 / gRPC :50052
│   │   ├── channel-service/         # :8083 / gRPC :50053
│   │   ├── guild-service/           # :8086 / gRPC :50056
│   │   ├── chat-service/            # :8084
│   │   ├── messaging-service/       # :8093
│   │   ├── realtime-service/        # :8092
│   │   └── (stubs: presence, media, notification, search, ai, voice)
│   ├── scripts/                     # Utility scripts
│   ├── postman/                     # API collections
│   └── docs/
│       ├── PATTERNS.md              # 19 code patterns reference
│       └── ROADMAP.md               # Development phases
├── hermes-fe/                       # Frontend (git submodule → github.com/bulutcan99/hermes-fe)
│   ├── src/                         # React + Vite + TypeScript
│   ├── docker-compose.yml           # Frontend container (Traefik-labeled)
│   ├── Dockerfile / Dockerfile.dev
│   └── vite.config.ts
└── hermes-vault/                    # This vault (knowledge base)
```

## Per-Service Code Structure

Every backend service follows the same DDD layout:

```
services/{name}/
├── src/
│   ├── domain/              # Pure business logic, entity, value objects, repository traits
│   ├── application/         # Use-case orchestration, services, events, ports
│   ├── infrastructure/      # SQLx repos, gRPC clients, NATS publishers, outbox
│   ├── presentation/        # HTTP handlers + routes, gRPC server, DTOs
│   ├── state/               # AppState composition
│   └── bootstrap/           # Startup wiring (AppBuilder)
├── migrations/              # SQLx per-service migrations
├── seeds/dev/               # Dev seed data
├── proto/                   # Service-specific .proto files
├── tests/                   # Integration tests (testcontainers)
└── build.rs                 # tonic-build proto codegen
```

## Key File Locations

| What | Path |
|---|---|
| Workspace Cargo.toml | `hermes-be/Cargo.toml` |
| Environment config | `hermes-be/.env` / `.env.example` |
| Traefik routes | `infra/traefik/dynamic/routes.yml` |
| Prometheus scrape | `hermes-be/infra/prometheus/prometheus.yml` |
| Grafana dashboards | `hermes-be/infra/grafana/provisioning/dashboards/` |
| Loki config | `hermes-be/infra/loki/loki-config.yml` |
| Tempo config | `hermes-be/infra/tempo/tempo.yaml` |
| Shared outbox code | `hermes-be/services/common/src/infrastructure/outbox/` |
| JWT manager | `hermes-be/services/common/src/infrastructure/security/jwt_manager.rs` |
| Request context | `hermes-be/services/common/src/observability/request_context.rs` |
| DB init script | `hermes-be/infra/postgres/init.sql` |
