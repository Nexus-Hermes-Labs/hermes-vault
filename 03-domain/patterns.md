# Code Patterns

Summary of the 19 canonical patterns. Full reference: `hermes-be/docs/PATTERNS.md`.

## Architecture Patterns

1. **Layered DDD (Hexagonal)**: 6 layers — domain → application → infrastructure → presentation → state → bootstrap. Dependencies flow inward only.
2. **Dependency Injection via Builder**: `AppBuilder::new().build_repositories().build_domain_services().build_application_services().build_state()`. No DI framework — explicit manual wiring.
3. **State Composition Root**: `AppState` groups related services under named sub-states (e.g., `GuildState`, `SharedState`).
4. **Service Bootstrap**: Every service follows the same startup: init_config → init_tracing → connect_db → connect_redis → init_metrics → AppBuilder → spawn tasks → start servers → await shutdown.

## Domain Patterns

5. **Repository Pattern**: Generic `Repository<T, ID>` in common. Domain-specific repos extend it. Traits in `domain/`, impls in `infrastructure/persistence/postgres/`.
6. **Unit of Work**: `UnitOfWork` base trait with `commit`/`rollback`. Per-service UoW adds writer accessors. `run_in_transaction(uow, closure)` helper ensures rollback on error. `SharedTx = Arc<Mutex<Option<Transaction>>>` for concurrent writer access.
7. **Aggregate + Value Objects**: Entities as aggregates with invariant-enforcing value objects (`Email`, `PasswordHash`, `Permissions`). Private constructors, validated on creation.
8. **Domain Events + Event Envelope**: `EventEnvelope<T>` carries metadata (event_id, aggregate_id, version, correlation_id, timestamp) alongside payload.

## Infrastructure Patterns

9. **Transactional Outbox**: DB write + event enqueued atomically. `OutboxPublisherTask` drains to JetStream. See `03-domain/outbox-pattern.md`.
10. **Trait-Based Abstractions**: All external I/O behind async traits (`EventPublisher`, `PasswordService`, `TokenHasher`, `EmailService`). Mockable for testing.
11. **Gateway Authentication**: JWT centralized at Traefik ForwardAuth. `RequestUser` extractor reads `X-User-Id/Role/Email` headers. `AdminOnly` / `ModeratorOrAbove` authorization extractors.

## Presentation Patterns

12. **DTO Pattern**: Request DTOs with `#[validate(...)]` + `#[derive(ToSchema)]`. Domain types never exposed directly in HTTP responses.
13. **Hierarchical Error Handling**: `DomainError → ApplicationError → ApiError`. Each with `From` impls. `thiserror` for derives. `unwrap`/`panic` denied workspace-wide.
14. **Pagination**: Standardized `PaginationParams` (clamped 1-100 page_size) → `Paginated<T>` response envelope.

## Operational Patterns

15. **Background Tasks**: Tokio-spawned with `watch::Receiver<bool>` for graceful shutdown. `tokio::select!` between work interval and shutdown signal.
16. **Graceful Shutdown**: `watch` channel broadcasts shutdown. HTTP server, gRPC server, and all background tasks respect it.
17. **OnceCell Config Singleton**: `Config::load()` runs once at startup, stored in `OnceCell`. Any layer calls `config()` for static reference.
18. **Workspace Lint Policy**: `unsafe_code` forbidden, `unwrap_used`/`expect_used`/`panic` denied, full clippy pedantic/nursery/cargo warnings.
