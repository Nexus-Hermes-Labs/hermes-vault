# Technology Stack

Quick reference for every technology Hermes depends on. For full rationale, see `hermes-be/docs/TECHNOLOGIES.md`.

## Backend

| Technology | Version | Purpose | Why This |
|---|---|---|---|
| Rust | stable | Language | Sub-ms latency, no GC pauses, compile-time safety |
| Tokio | 1.38 | Async runtime | Work-stealing scheduler, millions of concurrent tasks |
| Axum | 0.7 | HTTP framework | Extractor-based, Tower middleware, near-zero overhead |
| Tonic | 0.11 | gRPC | Type-safe inter-service communication, Protobuf codegen |
| SQLx | 0.8 | Database | Compile-time SQL verification, async Postgres |
| PostgreSQL | 16 | Database | ACID, UUID/JSONB types, mature ecosystem |
| Redis | 7 | Cache/Sessions | Sub-ms reads for sessions, presence, rate limits |
| NATS | 2.10 | Messaging | JetStream for at-least-once events, lightweight vs Kafka |
| jsonwebtoken | 9.3 | Auth | JWT HS256 (access: 6h, refresh: 30d) |
| Argon2 | 0.5 | Password hashing | Memory-hard, GPU/ASIC resistant |
| Figment | 0.10 | Config | Layer merging: .env → env vars |
| tracing | 0.1 | Observability | Structured spans for logs + distributed tracing |
| OpenTelemetry | 0.24 | Distributed tracing | W3C traceparent propagation → Tempo |
| metrics | 0.22 | Prometheus metrics | Request counters + latency histograms |
| utoipa | 5 | OpenAPI/Swagger | Auto-generated API docs per service |

## Frontend

| Technology | Purpose |
|---|---|
| React 19 | UI framework |
| Vite | Dev server + bundler (HMR) |
| TypeScript | Type safety |
| TanStack Router | Type-safe routing |
| TanStack Query | Server state / data fetching |
| Zustand | Client state (auth, UI) |
| Zod | Runtime schema validation |
| react-hook-form | Uncontrolled forms |
| Tailwind CSS v4 | Utility-first styling |
| Radix UI + shadcn/ui | Accessible component primitives |

## Infrastructure

| Technology | Purpose |
|---|---|
| Docker + Compose | Container orchestration (dev) |
| Traefik v3 | Edge proxy, ForwardAuth, rate limiting |
| Prometheus | Metrics scraping |
| Grafana Loki | Log aggregation |
| Promtail | Docker log collector |
| Grafana Tempo | Distributed trace storage |
| Grafana | Unified dashboards |
| Mailpit | Dev email catcher |

## Testing

| Technology | Purpose |
|---|---|
| testcontainers | Real Postgres/Redis in tests |
| mockall | Trait mocking for unit tests |
| fake + rstest | Test data generation |
| Schemathesis | OpenAPI-driven property testing |

## Notable Design Choices

- **No Consul in production path**: Was built, tested, and rolled back (May 2026) as over-engineered for pre-production. Code preserved on `feat/secrets-and-config-stores` branch. Config uses plain `.env` + env vars.
- **Tonic 0.11 + opentelemetry-otlp's internal Tonic 0.12**: Coexist as separate crates — different `http::Request` types require separate `Service` impls in `RequestIdScopeLayer`.
- **SHA-256 for token hashing, Argon2 for passwords**: Tokens have high entropy (rand-generated) so fast hash is sufficient; passwords need deliberate slowness.
