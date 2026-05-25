# Docker & Networking

## Compose Layout

The system is split across multiple compose files, all sharing `hermes-network`:

| Compose File | Contents |
|---|---|
| `hermes/docker-compose.yml` | Traefik + network creation |
| `hermes-be/docker-compose.yml` | All Rust services + stub whoami containers |
| `hermes-be/infra/docker-compose.yml` | Infrastructure (Postgres, Redis, NATS, Prometheus, Loki, Promtail, Tempo, Grafana, Mailpit) |
| `hermes-fe/docker-compose.yml` | Frontend container (Traefik-labeled) |

## Network

All containers run on `hermes-network` (external, created once by `make network`). Container DNS resolves service names directly:
- `hermes-auth-service:8081`
- `hermes-postgres:5432`
- etc.

## Service Discovery

- **Frontend**: Traefik discovers via Docker labels (`traefik.enable=true`)
- **Backend**: Defined in Traefik's file provider (`infra/traefik/dynamic/routes.yml`)
- **Infrastructure**: Direct container name resolution on shared network

## Stub Services

Unimplemented services use `traefik/whoami` as placeholder containers until built.

## Useful Commands

```bash
make dev        # Create network + start Traefik + infra + services + frontend + migrate
make up         # Start all (skip migrations)
make down       # Stop and remove
make clean      # Down + remove volumes
make logs       # Tail all logs
make logs-auth  # Auth service only
make db-migrate # Run all service migrations
make db-seed    # Seed dev data
make db-reset   # Clean + migrate + seed
```
