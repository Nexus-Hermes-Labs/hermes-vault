# ADR-002: Consul KV Built and Rolled Back

**Status**: Rolled back (May 2026)
**Branch**: `feat/secrets-and-config-stores` (code preserved)

## Context

With 13 services sharing common config (JWT secrets, DB URLs, NATS endpoints), duplicating values across `.env` files risks drift. Consul KV was implemented as a centralized config source.

## What Was Built

- Consul KV as a configuration layer (agent running in `-dev` mode)
- `common-config` read from Consul at startup via `ureq` (blocking HTTP client, safe in async context)
- Layer order: Consul shared → Consul per-service → `.env` → env vars (env wins)
- Migration script (`scripts/config-migration/migrate_to_consul.py`) to populate KV from `.env.example` files
- Consul UI at `:8500` for browsing/editing

## Decision

Rolled back as over-engineered for pre-production. The infrastructure complexity (running Consul, managing KV, no ACLs in dev mode, plaintext secrets) wasn't justified when the system is still single-host Docker Compose.

## Current State

- Config uses plain `.env` files + `figment` env-var layering
- No Consul references in `common-config` source code on `main`
- `TECHNOLOGIES.md` in `hermes-be/docs/` still references Consul (stale — needs update)

## Future

If/when the system moves to multi-host deployment, centralized config management should be revisited. Options: Consul with ACLs + TLS, HashiCorp Vault for secrets, or Kubernetes ConfigMaps/Secrets.
